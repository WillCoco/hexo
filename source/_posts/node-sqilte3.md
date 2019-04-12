---
title: node-sqilte3 api
tags: datebase
categories: [database, Javascript]
comments: true
date: 2018-01-16 19:10:24
---

### 打开数据库

      //数据库的名字是"mydatebase.db"
      var database;
      database = new sqlite3.Database("mydatebase.db", function(e){
       if (err) throw err;
      });
      //也可以使用内存型，数据不会永久保存
      database = new sqlite3.Database(":memory:", function(e){
       if (err) throw err;
      });

### db.run 执行一条语句

    run方法只执行第一条SQL语句！！！
    // 直接通过参数传值.
    db.run("UPDATE tbl SET name = ? WHERE id = ?", "bar", 2);

    // 将值封装为一个数组传值.
    db.run("UPDATE tbl SET name = ? WHERE id = ?", [ "bar", 2 ]);

    // 使用一个json传值.参数的前缀可以是“:name”，“@name”和“$name”。推荐用“$name”形式
    db.run("UPDATE tbl SET name = $name WHERE id = $id", {
     $id: 2,
     $name: "bar"
    });

### db.exec(sql) 执行多条语句
Database#exec与Database＃run函数一样，都是DDL和DML语句，但是Database#exec可以执行多条语句，并且不支持占位符参数。

### db.get 查询一条数据


### db.all 查询所有数据
! 注意，Database#all首先检索所有结果行并将其存储在内存中。 对于数据量可能很大的查询命令时候，请使用Database＃each函数或Database＃prepare代替这个方法

### db.each 遍历数据
与Database＃run函数相同，都是查询多条数据，但是具有以下区别：
回调的签名是function（err，row）。如果结果集成功但为空，则不会调用回调。对于每个检索到的行，该方法都会调用一次回调。执行顺序与结果集中的行顺序完全对应。
调用所有行回调后，如果存在complete回调函数，将调用这个回调。第一个参数是一个错误对象，第二个参数是检索行数。

### 流程控制
sqlite3的API都是异步的，流程控制通过[db.serialize](#db.serialize) 和 [db.parallelize](#db.parallelize)。

### db.serialize

      // 这里执行的命令是并行的
      db.serialize(function() {
       // 这里执行的命令是串行的
         db.serialize(function() {
         // 这里执行的命令是串行的
         });
       // 这里执行的命令是串行的
      });
      // 这里执行的命令是并行的

### db.parallelize

      // 这里执行的命令是并行的
      db.serialize(function() {
       // 这里执行的命令是串行的
         db.parallelize(function() {
         // 这里执行的命令是并行的
         });
       // 这里执行的命令是串行的
      });
      // 这里执行的命令是并行的

### db.prepare 预编译SQL
可以预编译sql语句，执行的时候再链接具体参数。这样的好处是可以减少sql语句被编译的次数。
Database#prepare执行后，会返回一个命令对象，这个命令对象可以反复执行。这个对象的api：

      statement.get
      statement.run
      statement.all
      statement.each

与db同名方法调用方式相同。但在这里是可以复用的，避免重新变异sql语句。
! 注意，这些方法的param参数都会对Statement对象绑定参数，在下一次执行的时候，如果没有重新绑定参数，是会使用上一次参数的。

### 绑定参数 bind

Statement#bind([param, ...], [callback])

Database#prepare执行的时候，是可以绑定参数的。不过使用此方法可以全重置语句对象和行游标，并删除所有先前绑定的参数，实现重新绑定的功能。

### 重置语句的行游标 reset

Statement.reset([callback])

重置语句的行游标，并保留参数绑定。使用此功能可以使用相同的绑定重新执行相同的查询。


### 数据库事务
事务是关系型数据库中的一个重要部分，sqlite自然也是支持事务的，但是sqlite3并没有提供特殊API去实现的事务相关的操作，只能靠SQL语句去控制事务。这里举一个事务相关的例子。


      var db = new sqlite3.Database(db_path);
      db.run("CREATE TABLE foo (id INT, txt TEXT)");
      db.run("BEGIN TRANSACTION");
      var stmt = db.prepare("INSERT INTO foo VALUES(?, ?)");
      for (var i = 0; i < count; i++) {
         stmt.run(i, randomString());
      }
      db.run("COMMIT TRANSACTION");
