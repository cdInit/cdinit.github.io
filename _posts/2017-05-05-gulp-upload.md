---
layout: post
title:  "使用gulp实现前端自动化部署"
categories: gulp
tags: gulp
author: Init
---

* content
{:toc}

在使用vue编译后需要上传到服务器，之前用的sftp客户端来上传，比较麻烦。现在使用gulp实现自动化部署。





## 1、npm依赖文件

``` sh
npm install --save-dev stream-combiner2
npm install --save-dev gulp-uglify
npm install --save-dev gulp
npm install --save-dev minimist
npm install --save-dev gulp-ssh
npm install --save-dev del
npm install -g gulp
```

使用到的主要是gulp和gulp-ssh

## 2、创建ssh配置文件 config.kmbk.js

``` sh
var appName = 'wxdc';
module.exports = {
    version: '1.0.0',
    env: 'kmbk server',
    //上传配置
    ssh: {
        host: 'xxxx.xxxxx.top',
        port: 22,
        username: 'xxxx',
        password: 'xxxxx',
    },
    remoteDir: `/storage/www/html/${appName}`,
    commands: [
        //删除现有文件
        `rm -rf /storage/www/html/${appName}/static/`,
        `rm -f /storage/www/html/${appName}/index.html`
    ]
};

```

## 3、创建gulpfile.js

``` sh

var combiner = require('stream-combiner2');
var uglify = require('gulp-uglify');
var gulp = require('gulp');
var del = require('del');
var minimist = require('minimist');
var GulpSSH = require('gulp-ssh');
//获取通过命令行传进来的值
var knownOptions = {
  string: 'env',
  default: { env: process.env.NODE_ENV || 'production' }
};
var options = minimist(process.argv.slice(2), knownOptions);
var env = options.env;
//载入配置文件
var config = require(`./profile/config.${env}.js`);
var sshConfig = config.ssh;
//打开ssh通道
var gulpSSH = new GulpSSH({
    ignoreErrors: false,
    sshConfig: sshConfig
});

console.log(sshConfig);

gulp.task('default', ['deployFile'], function() {
  // 将你的默认的任务代码放在这
  
});

/**
 * 上传文件
 */
gulp.task('deployFile', ['execSSH'],() => {
    console.log('5s后开始上传文件...');
    setTimeout(function(){
        return gulp
            .src(['./dist/**', '!**/node_modules/**'])
            .pipe(gulpSSH.dest(config.remoteDir));
    },5000);
    
});

/**
 * 执行命令
 */
gulp.task('execSSH', () => {
    console.log('删除服务器上现有文件...');
    return gulpSSH.shell(config.commands, {filePath: 'commands.log'})
        .pipe(gulp.dest('logs'));
});

/**
 * 将src/bootstrap/js/*.js打包到public/bootstrap目录下，并将js代码压缩+混淆
 */
// gulp.task('moveAndCompressAndUglify', () => {
//     console.log('hello gulp');
//     var combined = combiner.obj([
//         gulp.src('src/bootstrap/js/*.js'),
//         uglify(),
//         gulp.dest('public/bootstrap')
//     ]);
// });

/**
 * 删除文件
 */
// gulp.task('deleteFile', () => {
//     del([
//         'dist/report.csv',
//         // 这里我们使用一个通配模式来匹配 `mobile` 文件夹中的所有东西
//         'dist/mobile/**/*',
//         // 我们不希望删掉这个文件，所以我们取反这个匹配模式
//         '!dist/mobile/deploy.json'
//     ], cb);
// });

```

## 创建一个bat执行命令 publish_to_kmbk.bat

``` sh
call gulp --env kmbk
```

以后每次编译之后，只需要执行publish_to_kmbk.bat就行了。