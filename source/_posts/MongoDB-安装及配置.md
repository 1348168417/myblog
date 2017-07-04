---
title: MongoDB_安装及配置
date: 2017-06-01 23:44:28
tags:
---
MongoDB 下载：https://www.mongodb.com/download-center#community

## MongoDB 安装配置： 
#### 备注：记得加入环境变量，指定绝对路径，最好是以超级管理员运行cmd

### 1、进入mongodb 下的目录
E:\Tool\modb>
在目录下新建
 - db 目录
 - log 目录 
 - 在log 目录下新建mongodb.log 文件<!-- more -->

### 2、指定日志文件目录
~~~
E:\Tool\modb\bin>mongod --logpath "E:\Tool\modb\log\mongodb.log"指定数据存放目录
~~~

### 3、指定数据存放目录
~~~
E:\Tool\modb\bin>mongod --dbpath "E:\Tool\modb\db"
~~~
~~~
2017-04-09T10:45:31.583+0800 I CONTROL  [initandlisten] MongoDB starting : pid=12244 port=27017 dbpath=E:\Tool\modb\db 64-bit host=JSB015
2017-04-09T10:45:31.584+0800 I CONTROL  [initandlisten] targetMinOS: Windows Vista/Windows Server 2008
2017-04-09T10:45:31.587+0800 I CONTROL  [initandlisten] db version v3.4.3
2017-04-09T10:45:31.587+0800 I CONTROL  [initandlisten] git version: f07437fb5a6cca07c10bafa78365456eb1d6d5e1
2017-04-09T10:45:31.588+0800 I CONTROL  [initandlisten] allocator: tcmalloc
2017-04-09T10:45:31.589+0800 I CONTROL  [initandlisten] modules: none
2017-04-09T10:45:31.590+0800 I CONTROL  [initandlisten] build environment:
2017-04-09T10:45:31.590+0800 I CONTROL  [initandlisten]     distarch: x86_64
2017-04-09T10:45:31.591+0800 I CONTROL  [initandlisten]     target_arch: x86_64
2017-04-09T10:45:31.592+0800 I CONTROL  [initandlisten] options: { storage: { dbPath: "E:\Tool\modb\db" } }
2017-04-09T10:45:31.600+0800 I STORAGE  [initandlisten] wiredtiger_open config: create,cache_size=3518M,session_max=20000,eviction=(threads_min=4,threads_max=4),config_base=false,statistics=(fast),log=(enabled=true,archive=true,path=journal,compressor=snappy),file_manager=(close_idle_time=100000),checkpoint=(wait=60,log_size=2GB),statistics_log=(wait=0),
2017-04-09T10:45:31.630+0800 I CONTROL  [initandlisten]
2017-04-09T10:45:31.630+0800 I CONTROL  [initandlisten] ** WARNING: Access control is not enabled for the database.
2017-04-09T10:45:31.631+0800 I CONTROL  [initandlisten] **          Read and write access to data and configuration is unrestricted.
2017-04-09T10:45:31.632+0800 I CONTROL  [initandlisten]
2017-04-09T10:45:32.047+0800 I FTDC     [initandlisten] Initializing full-time diagnostic data capture with directory 'E:/Tool/modb/db/diagnostic.data'
2017-04-09T10:45:32.058+0800 I INDEX    [initandlisten] build index on: admin.system.version properties: { v: 2, key: { version: 1 }, name: "incompatible_with_version_32", ns: "admin.system.version" }
2017-04-09T10:45:32.058+0800 I INDEX    [initandlisten]          building index using bulk method; build may temporarily use up to 500 megabytes of RAM
2017-04-09T10:45:32.061+0800 I INDEX    [initandlisten] build index done.  scanned 0 total records. 0 secs
2017-04-09T10:45:32.063+0800 I COMMAND  [initandlisten] setting featureCompatibilityVersion to 3.4
2017-04-09T10:45:32.064+0800 I NETWORK  [thread1] waiting for connections on port 27017
2017-04-09T10:47:24.020+0800 I CONTROL  [thread2] Ctrl-C signal
2017-04-09T10:47:24.020+0800 I CONTROL  [consoleTerminate] got CTRL_C_EVENT, will terminate after current cmd ends
2017-04-09T10:47:24.028+0800 I NETWORK  [consoleTerminate] shutdown: going to close listening sockets...
2017-04-09T10:47:24.030+0800 I NETWORK  [consoleTerminate] closing listening socket: 464
2017-04-09T10:47:24.032+0800 I NETWORK  [consoleTerminate] shutdown: going to flush diaglog...
2017-04-09T10:47:24.034+0800 I FTDC     [consoleTerminate] Shutting down full-time diagnostic data capture
2017-04-09T10:47:24.043+0800 I STORAGE  [consoleTerminate] WiredTigerKVEngine shutting down
2017-04-09T10:47:24.164+0800 I STORAGE  [consoleTerminate] shutdown: removing fs lock...
2017-04-09T10:47:24.165+0800 I CONTROL  [consoleTerminate] now exiting
2017-04-09T10:47:24.171+0800 I CONTROL  [consoleTerminate] shutting down with code:12
~~~
4、连接数据库
~~~
E:\Tool\modb\bin>mongo
MongoDB shell version v3.4.3
connecting to: mongodb://127.0.0.1:27017
2017-04-09T10:47:38.944+0800 W NETWORK  [thread1] Failed to connect to 127.0.0.1:27017 after 5000ms milliseconds, giving up.
2017-04-09T10:47:38.946+0800 E QUERY    [thread1] Error: couldn't connect to server 127.0.0.1:27017, connection attempt failed :
connect@src/mongo/shell/mongo.js:237:13
@(connect):1:6
exception: connect failed
~~~
5、从新打开一个终端, 安装MongoDB服务
~~~
E:\Tool\modb>cd bin
~~~

~~~
E:\Tool\modb\bin>mongod --dbpath "E:\Tool\modb" --logpath "E:\Tool\modb\log\mongodb.log" --install --serviceName "MongoDB"
2017-04-09T11:01:31.833+0800 I CONTROL  [main] log file "E:\Tool\modb\log\mongodb.log" exists; moved to "E:\Tool\modb\log\mongodb.log.2017-04-09T03-01-31".
~~~

~~~
E:\Tool\modb\bin>net start MongoDB
MongoDB 服务正在启动 .
MongoDB 服务已经启动成功。
~~~

~~~
E:\Tool\modb\bin>mongo
MongoDB shell version v3.4.3
connecting to: mongodb://127.0.0.1:27017
MongoDB server version: 3.4.3
Welcome to the MongoDB shell.
For interactive help, type "help".
For more comprehensive documentation, see
        http://docs.mongodb.org/
Questions? Try the support group
        http://groups.google.com/group/mongodb-user
Server has startup warnings:
2017-04-09T11:02:18.826+0800 I CONTROL  [initandlisten]
2017-04-09T11:02:18.826+0800 I CONTROL  [initandlisten] ** WARNING: Access control is not enabled for the database.
2017-04-09T11:02:18.826+0800 I CONTROL  [initandlisten] **          Read and write access to data and configuration is unrestricted.
2017-04-09T11:02:18.826+0800 I CONTROL  [initandlisten]
~~~

连接上了———————