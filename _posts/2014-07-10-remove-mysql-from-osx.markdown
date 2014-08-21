---
layout: post
title: "從OSX手動移除MySQL"
date: 2014-07-10 00:13:17 +0800
comments: true
categories: [OSX, SQL]
---
從OSX手動移除MySQL...

* 資料來源：
  1. from http://johnmcostaiii.net/2011/removing-mysql-osx-lion/
  1. from http://mini.nidbox.com/diary/read/7485055

<!-- more -->
``` bash
sudo rm /usr/local/mysql
sudo rm -rf /usr/local/mysql*
sudo rm -rf /var/db/receipts/com.mysql.*

sudo rm -rf /Library/StartupItems/MySQLCOM
sudo rm -rf /Library/PreferencePanes/MySQL*
rm -rf ~/Library/PreferencePanes/MySQL*

sudo rm -rf /Library/Receipts/mysql*
sudo rm -rf /Library/Receipts/MySQL*

sudo rm /etc/my.cnf

sudo vi /etc/hostconfig   # 移除 MYSQLCOM=-YES-
```
另外，安裝記錄在`/Library/Receipts/InstallHistory.plist`
