https://github.com/malone231214/gpthub

# GPT应用-使用中文操作数据库

本次尝试使用langchain来操作数据库；

## 环境配置
下面是数据库相关的表，使用Mysql5.7 数据库,数据库名students

下面是相关表的介绍

学生表，有名字、分数、和老师的备注

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/67419feb5fb24c03b51eeb9f0181a21b~tplv-k3u1fbpfcp-watermark.image?)

学生父母表，其中有学生的名字，父母的电话

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/54e89f905ec84bf9871c8eb6d4682e99~tplv-k3u1fbpfcp-watermark.image?)

## 代码介绍

下面是notebook 内容的相关介绍

安装langchain和pymysql依赖

```
%pip install langchain
%pip install pymysql
```

配置OPENAI_API_KEY，在openAI获取key

```
%env OPENAI_API_KEY=sk-d8X5qdQlAnTdOHzCiOdZT3B
```

引入依赖

```
from langchain import OpenAI, SQLDatabase, SQLDatabaseChain
```

配置数据库连接

```
db = SQLDatabase.from_uri("mysql+pymysql://root:root@127.0.0.1/students")
llm = OpenAI(temperature=0)
```

创建SQLDatabaseChain，chain是langchain中的一个重要概念，可以从他的官网了解，例如我们可以将接口访问封装成一个Chain等。

这里我们使用的是langchain提供的访问数据库的Chain，官方还提供了更多的chain。

```
db_chain = SQLDatabaseChain(llm=llm, database=db, verbose=True)
```

开始询问，我们直接使用中文问，因为我们配置了`verbose=True`,所以他将中间产生的SQL也输出了

```
db_chain.run("全班有多少个学生?")
```


![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bbe1e0bddd794136a52b39ffdf2d4ae6~tplv-k3u1fbpfcp-watermark.image?)

```
db_chain.run("他们的名字是什么?")
```

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4f07ca75ee65457a8fb2342077534899~tplv-k3u1fbpfcp-watermark.image?)
```
db_chain.run("他们的平均分是多少?")
```
![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/99ef7b9f582e443e8f8d9c8aeb07a225~tplv-k3u1fbpfcp-watermark.image?)
```
db_chain.run("去除0分之后的平均分是多少")
```

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f1543b7655b342ffaf16138ec36e8bd6~tplv-k3u1fbpfcp-watermark.image?)
```
db_chain.run("谁的了0分?")
```

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/01db91ede94f454593c7be7ebaa767cd~tplv-k3u1fbpfcp-watermark.image?)


```
db_chain.run("谁的了0分以及为什么")
```

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/98e34735843a4673a246b9c46eb9dfbb~tplv-k3u1fbpfcp-watermark.image?)

```
db_chain.run("说得了0分，给出父母的电话")
```

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f37d865b94b64464b84687596e6e99e6~tplv-k3u1fbpfcp-watermark.image?)

通过以上的实例演示，能够看到非常好的效果，大家可以多测试，欢迎交流学习

## SQL

```
DROP TABLE IF EXISTS `parents`;

CREATE TABLE `parents` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
  `student_name` varchar(20) DEFAULT '',
  `parent_name` varchar(20) DEFAULT NULL,
  `parent_mobile` varchar(10) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

LOCK TABLES `parents` WRITE;
/*!40000 ALTER TABLE `parents` DISABLE KEYS */;

INSERT INTO `parents` (`id`, `student_name`, `parent_name`, `parent_mobile`)
VALUES
    (1,'Alex','Barry','0881234567'),
    (2,'Alice','Jessica','0891234567'),
    (3,'Jack','Simon','0876666666'),
    (5,'Ophelia','Tracy','0881111111');

/*!40000 ALTER TABLE `parents` ENABLE KEYS */;
UNLOCK TABLES;


# Dump of table students
# ------------------------------------------------------------

DROP TABLE IF EXISTS `students`;

CREATE TABLE `students` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
  `name` varchar(11) DEFAULT NULL,
  `score` int(11) DEFAULT NULL,
  `teacher_note` text,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

LOCK TABLES `students` WRITE;
/*!40000 ALTER TABLE `students` DISABLE KEYS */;

INSERT INTO `students` (`id`, `name`, `score`, `teacher_note`)
VALUES
    (1,'Alex',100,'Alex did perfectly every day in the class. There is no surprise he got the full mark.'),
    (2,'Alice',70,'Alice needs a lot of improvements.'),
    (3,'Jack',75,'Even it's not the best, Jack has already improved. Keep going.'),
    (4,'Ophelia',0,'Unfortunately, Ophelia missed the test.'),
    (5,'Zack',60,'Zack needs to do better.');

/*!40000 ALTER TABLE `students` ENABLE KEYS */;
UNLOCK TABLES;
```
更多实例参考：https://github.com/malone231214/gpthub

## 参考

-   <https://python.langchain.com/en/latest/modules/chains/examples/sqlite.html>
-   <https://github.com/sugarforever/LangChain-SQL-Chain>