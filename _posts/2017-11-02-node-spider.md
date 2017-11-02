---
layout: post
title:  "制作简单的NODE爬虫"
categories: node
tags: node
author: Init
---

* content
{:toc}

> 由于想把博客上面的文章扒下来，所以动手写一个爬虫，基于node的。





## 创建工程

### 使用express-generator初始化一个项目。

``` sh
npm install express-generator -g

express -h

express project-name  // 生成project-name项目

就这么简单。

```

### npm 安装依赖

``` sh
// package.json
{
  "name": "spider",
  "version": "0.0.0",
  "private": true,
  "scripts": {
    "start": "node ./bin/www",
    "hot": "supervisor ./bin/www"
  },
  "dependencies": {
    "body-parser": "~1.17.1",
    "cheerio": "^1.0.0-rc.2",
    "cookie-parser": "~1.4.3",
    "debug": "~2.6.3",
    "express": "~4.15.2",
    "jade": "~1.11.0",
    "morgan": "~1.8.1",
    "request": "^2.83.0",
    "serve-favicon": "~2.4.2"
  }
}
```

### 编写爬虫

思路：

> 1.使用request模块模拟请求数据

> 2.使用cheerio加载，使其能够像操作DOM一样操作请求过来的数据

> 3.将数据保存到数据库。

> 4.OVER

``` sh
var express = require('express');
var router = express.Router();

var http = require('http');
var fs = require('fs');
var cheerio = require('cheerio');
var request = require('request');

var qs = require('querystring');

var pageStart = 2;
var i = 0;
var articleUrls = [];
var url = 'http://www.cdinit.com/'

//获取第一页的文章链接
function getFirstPage() {
	var firstpage = new Promise(function (res, rej) {
		request('http://www.cdinit.com', function (error, response, body) {
			if (!error && response.statusCode == 200) {
				$ = cheerio.load(body);
				var urls = $('.post-link')
				var hrefs = [];
				for (var i = 0; i < urls.length; i++) {
					hrefs.push('http://www.cdinit.com' + $(urls[i]).attr('href'))
				}
				res(hrefs)
			}
		})
	})
	return firstpage;
}

//获取不是第一页的文章链接
function getOtherPage(i) {
	var otherpage = new Promise(function (res, rej) {
		request('http://www.cdinit.com/page' + i, function (error, response, body) {
			if (!error && response.statusCode == 200) {
				$ = cheerio.load(body);
				var urls = $('.post-link')
				var hrefs = [];
				for (var i = 0; i < urls.length; i++) {
					hrefs.push('http://www.cdinit.com' + $(urls[i]).attr('href'))
				}
				res(hrefs)
			}
		})
	})
	return otherpage;
}

//递归getOtherPage方法，获取不是第一页的文章链接
function loop(max, cb) {
	var task = new Promise(function (re, rj) {
		if (max < 8) {
			console.log('loop start ' + max + '...')
			getOtherPage(max).then(o => {
				articleUrls = articleUrls.concat(o);
				max++;
				loop(max, cb);
			})
		} else {
			console.log('loop end ...')
			cb();
			re();
		}
	})
}

//根据链接获取文章详情
function getPageDetail(itemUrl) {
	var pageDetail = new Promise(function (res, rej) {
		request(itemUrl, function (error, response, body) {
			response.setEncoding('utf-8');
			if (!error && response.statusCode == 200) {
				$ = cheerio.load(body, { decodeEntities: false });
				var detail = {
					title: $('h1').text(),
					info: $('article').text().trim().slice(0, 200),
					createTime: $('.label-card').eq(0).text().trim(),
					content: $('article').text().trim(),
					columnid: '1508990521075'
				}
				res(detail)
			}
		})
	})
	return pageDetail;
}

//访问根目录执行爬虫
router.get('/', function (req, res, next) {
	getFirstPage().then(function (firstpage) {
		articleUrls = articleUrls.concat(firstpage);
	}).then(other => {
		loop(pageStart, function () {
			articleUrls.forEach(item => {
				//取每个的详情并写入数据库
				getPageDetail(item).then(detail => {
					var data = detail;
					data = qs.stringify(data);
					var post_options = {
						host: 'localhost',
						port: 3000,
						path: '/article/add',
						method: 'POST',
						headers: {
							'Content-Type': 'application/x-www-form-urlencoded',
							'Authorization': 'Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImluaXQiLCJpYXQiOjE1MDg5MjQzMTIsImV4cCI6MTUwOTA1MzkxMn0.QK_cQK3rBHk6oDXE3x5QvI0wLUdKa8SUTzZvq5Dp9ls'
						}
					};
					
					var post_req = http.request(post_options, function (res) {
						console.log('STATUS: ' + res.statusCode);  
						console.log('HEADERS: ' + JSON.stringify(res.headers));  
						res.setEncoding('utf8');  
						res.on('data', function (chunk) {  
							console.log('BODY: ' + chunk);  
						});  
					});

					post_req.on('error', function (e) {  
						console.log('problem with request: ' + e.message);  
					});
					// post the data
					post_req.write(data);  
					post_req.end(); 

				})
			})
			res.json({ "urls": articleUrls })
		});
	})
});

module.exports = router;

```