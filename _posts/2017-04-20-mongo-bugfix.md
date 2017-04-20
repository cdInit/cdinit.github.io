---
layout: post
title:  "Node中使用Promise执行mongo语句"
categories: mongodb
tags: mongodb
author: Init
---

* content
{:toc}

在开发中遇到一个这样的问题，当所有查询执行完成后再处理查询结果回显到页面，而且所有查询都是异步的，所以需要promise来辅助完成





## 直接看代码

``` sh

router.get('/paysum.node', function(req, res, next) {
    var msg = {
        stats: 1,
        err: [],
        main: {},
        mark: ''
    };

    var params = dealParams(req);
    var merchantId = req.query.merchantId;
    var branchId = req.query.branchId;

    //查询出来的数据
    var paywayList = [];
    //交易产生的
    var tradeList = [];
    //充值产生
    var rechargeList = [];
    //交易产生的统计数据
    var tradeSum = {};
    //充值产生的统计数据
    var rechargeSum = {};
    //会员充值次数
    var vipRechargeCount = 0;

    var task1 = new Promise ((resolve,reject)=>{
        //首先查询该商户下的所有支付名称
        bossService.getPaywayListByMerchant(merchantId, branchId, function(items) {
            paywayList = items;
            Log.debug('task1 endding...');
            return resolve();
        });
    })
    var task2 = new Promise((resolve,reject)=>{
        //然后查询出该商户下交易产生的支付数据列表和统计数据
        bossService.getPaysumListByMerchant(merchantId, branchId, params.startDate, params.endDate, function(items) {
            tradeList = items;
            Log.debug('task2 endding...');
            return resolve();
        });
    })
    var task3 = new Promise((resolve,reject)=>{
        //最后在查询出该商户下充值产生的列表和统计数据
        bossService.getVippaysumListByMerchant(merchantId, branchId, params.startDate, params.endDate, function(items, objs) {
            rechargeList = objs;
            Log.debug('task3 endding...');
            return resolve();
        });
    })
    
    var task4 = new Promise((resolve,reject)=>{
        bossService.getDaysumListByMerchant(merchantId, branchId, params.startDate, params.endDate, function(list, sum) {
            tradeSum = sum;
            Log.debug('task4 endding...');
            return resolve();
        });
    })
    var task5 = new Promise((resolve,reject)=>{
        bossService.getRechargeListByMerchant(merchantId, branchId, params.startDate, params.endDate, function(list, sum) {
            rechargeSum = sum;
            Log.debug('task5 endding...');
            return resolve();
        });
    })
    var task6 = new Promise((resolve,reject)=>{
        //查询会员充值次数
        bossService.getVipRechargeSum(merchantId, branchId, params.startDate, params.endDate, function(count) {
            vipRechargeCount = count;
            Log.debug('task6 endding...');
            return resolve();
        });
    })

    Promise.all([task1, task2, task3, task4, task5, task6]).then(() => {
        //到这里查询已全部结束
        //最后合并该统计数据
        Log.debug('promise all start...')
        var payList = [];
        tradeList.forEach(function(value) {
            var recharge = rechargeList[value.payId];
            if (recharge) {
                value.payAmt += recharge.payAmt;
                //封装最新的payname
                value.payWay = paywayList[value.payId]
                delete rechargeList[value.payId];
            }
        });
        for (var key in rechargeList) {
            var obj = rechargeList[key];
            obj.payWay = paywayList[obj.payId];
            tradeList.push(obj);
        }
        tradeList.sort((obj1, obj2) => (obj2.payAmt - obj1.payAmt));
        //还差统计数据
        tradeSum.saleAmt += rechargeSum.amt;
        tradeSum.vipAmt = rechargeSum.amt;
        tradeSum.rechargeCount = vipRechargeCount || 0;
        msg.main.sum = tradeSum;
        msg.main.pay = tradeList;
        if (msg.main.sum.saleAmt == 0) {
            msg.main = null;
        }
        res.send(msg);
    });

});

```

