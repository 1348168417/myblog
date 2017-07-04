---
title: 浅谈MyEclipse通过JDBC连接MySQL数据库
date: 2017-05-19 10:00:18
tags:
---
1.前提是MyEclipse已经能正常开发Java工程 
2.安装MySQL 
个人使用的是版本是 mysql-5.0.22-win32.zip 
网址：http://www.mysql.com/downloads/mysql/#downloads 
3.下载JDBC驱动 
个人使用的是 mysql-connector-java-5.1.22.zip，所需要的就是解压缩之后其中的 mysql-connector-java-5.1.22-bin.jar 
网址：http://www.mysql.com/downloads/connector/j/ 
4.代码测试 
复制代码 代码如下:<!-- more -->
~~~jdbc
package ts.jsj.lyh; 

import java.sql.*; 

/** *//** 
* 使用JDBC连接数据库MySQL的过程 
* DataBase：JSJ， table：student； 
* @author DuChangfeng 2008 09 18 
*/ 
public class JDBCTest { 

public static Connection getConnection() throws SQLException, 
java.lang.ClassNotFoundException 
{ 
//第一步：加载MySQL的JDBC的驱动 
Class.forName("com.mysql.jdbc.Driver"); 

//取得连接的url,能访问MySQL数据库的用户名,密码；jsj：数据库名 
String url = "jdbc:mysql://localhost:3306/jsj"; 
String username = "root"; 
String password = "111"; 

//第二步：创建与MySQL数据库的连接类的实例 
Connection con = DriverManager.getConnection(url, username, password); 
return con; 
} 


public static void main(String args[]) { 
try 
{ 
//第三步：获取连接类实例con，用con创建Statement对象类实例 sql_statement 
Connection con = getConnection(); 
Statement sql_statement = con.createStatement(); 

/** *//************ 对数据库进行相关操作 ************/ 
//如果同名数据库存在，删除 
//sql_statement.executeUpdate("drop table if exists student"); 
//执行了一个sql语句生成了一个名为student的表 
//sql_statement.executeUpdate("create table student (id int not null auto_increment, name varchar(20) not null default 'name', math int not null default 60, primary key (id) ); "); 
//向表中插入数据 
//sql_statement.executeUpdate("insert student values(1, 'liying', 98)"); 
//sql_statement.executeUpdate("insert student values(2, 'jiangshan', 88)"); 
//sql_statement.executeUpdate("insert student values(3, 'wangjiawu', 78)"); 
//sql_statement.executeUpdate("insert student values(4, 'duchangfeng', 100)"); 
//---以上操作不实用，但是列出来作为参考--- 

//第四步：执行查询，用ResultSet类的对象，返回查询的结果 
String query = "select * from student"; 
ResultSet result = sql_statement.executeQuery(query); 
/** *//************ 对数据库进行相关操作 ************/ 

System.out.println("Student表中的数据如下:"); 
System.out.println("------------------------"); 
System.out.println("学号" + " " + "姓名" + " " + "数据成绩 "); 
System.out.println("------------------------"); 

//对获得的查询结果进行处理，对Result类的对象进行操作 
while (result.next()) 
{ 
int number = result.getInt("sno"); 
String name = result.getString("sname"); 
String mathScore = result.getString("sgrade"); 
//取得数据库中的数据 
System.out.println(" " + number + " " + name + " " + mathScore); 
} 

//关闭连接和声明 
sql_statement.close(); 
con.close(); 

} catch(java.lang.ClassNotFoundException e) { 
//加载JDBC错误,所要用的驱动没有找到 
System.err.print("ClassNotFoundException"); 
//其他错误 
System.err.println(e.getMessage()); 
} catch (SQLException ex) { 
//显示数据库连接错误或查询错误 
System.err.println("SQLException: " + ex.getMessage()); 
} 
} 

} 
~~~
### 以上大部分内容整理自网络，感谢猿猿们的无私奉献~~具体的步骤、强大的互联网上都比较容易查询的到，这里不再赘述，现加上几点个人认为需要注意的地方： 

* 1）关于mysql-connector-java-5.1.22-bin.jar 的存放位置。在MyEclipse具体的java工程中新建一存放jar 包的文件夹（如 lib），将mysql-connector-java-5.1.22-bin.jar 复制到文件夹中，选中jar包右击--->Build Path--->Add To Build Path，即可。 

若出现 

ClassNotFoundExceptioncom.mysql.jdbc.Driver 

的提示，则正是由于缺少导入jar包所造成的。 

* 2）如果已经对MySQL的使用很熟悉，则可忽略这条。个人在测试连接时，老是出现这样的异常提示： 

SQLException: Communications link failure 
The last packet sent successfully to the server was 0 milliseconds ago. The driver has not received any packets from the server. 

这正是由于个人对MySQL使用不熟悉，对MySQL进行了诸多尝试性的操作，不知何时无意中将MySQL的服务(如果在安装MySQL时没有更改的话，缺省服务名就是MySQL)关闭，解决方法开启此服务即可。控制面板--->管理工具--->服务--->MySQL--->选择启用。 

* 3)在使用上面的代码测试时，需要更改的地方有： 
//MySQL数据库的用户名,密码,数据库名 
复制代码 代码如下:

String url = "jdbc:mysql://localhost:3306/jsj"; 
String username = "root"; 
String password = "111"; 

以及具体基本表中的所要查询的字段名： 
复制代码 代码如下:

int number = result.getInt("sno"); 
String name = result.getString("sname"); 
String mathScore = result.getString("sgrade"); 

多多分享，有问题欢迎交流~~