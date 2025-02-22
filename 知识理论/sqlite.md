# Sqlite数据库的基本使用

### 0-0数据库是什么

> 借用百科上的话，为数据库是“按照数据结构来组织、存储和管理数据的仓库”。是一个长期存储在计算机内的、有组织的、可共享的、统一管理的大量数据的集合。
> 人话来说就是很多个数据存储在一个文件里

## 数据库都分为哪几个类型

### 1-0在搞清楚什么是非关系型和关系型之前我们需要搞清一个概念 -字段-
> 字段就如同python的键值对一样，比如有一个字段name，理所当然他的属性是str，这就是字段其实就是键值对

### 1-1正式开始

> 关系型数据库：存储的格式可以直观地反映实体间的关系。关系型数据库和常见的表格比较相似，关系型数据库中表与表之间是有很多复杂的关联关系的。
> 人话来说就是像表格一样每个表和数据都有关系，例如MySQL还有本教程教的sqlite

> 非关系型数据库：例如，以键值对存储，且结构不固定，每一个元组可以有不一样的字段，每个元组可以根据需要增加一些自己的键值对，
> 这样就不会局限于固定的结构，可以减少一些时间和空间的开销。

> 举个栗子，比如我向非关系型数据库存储了一个对象，他的字段分别为，name，grade，并给了他一个对象a，这时我又存储进了一个对象b，有old这个字段，这时
> 数据库里就有了a，b两个对象来分别访问

> 这个为关系型数据库，首先我们需要创建一张表，表就是多个相同对象的集合，创建一个表a，分别有name，old两个字段，而想要向表a存储对象，需要实例化所
> 有表a所有的字段，才能存储

## 通过一个普通python代码来带入数据库的世界

### 2-0创建数据库
> 在文件夹中创建一个后缀为sqlite3的文件
>
> 然后可以使用pycharm的数据库功能访问   (如图)
>
> 点击确定
![image](https://github.com/MRSlouzk/Nonebot-plugintutorials/blob/main/imgs/sqlite%E5%88%9B%E5%BB%BA.png)

### 2-1创建表
> 在data.sqlite3下会有main，而main里会有表这个，右键表，会弹出来下面的选项
![image](https://github.com/MRSlouzk/Nonebot-plugintutorials/blob/main/imgs/sqlite%E5%88%9B%E5%BB%BA%E8%A1%A8.png)

> 接下来创建表，这个表中有五个字段，分别为studentID name old grade class这五个
> 你会发现我把studentID设置为主键，非null，唯一，自动增加那这些都是什么意思捏？
> 
> 主键：能唯一表达这个表中的每一项
> 
> 非null：就是不为null，什么你问我null是什么？百度下()
> 
> 唯一：就是表面意思
> 
> 自动增加：如果类型是integer，会自动+1
> 但你又会发现text，integer这些类型，接下来我们会讲sqlite的基本类型
> 
> NULL：表示值为空(null)值
> 
> INTEGER：表示值是一个有符号整数，根据值的大小存储在1,2,3,4,6或8个字节中
> 
> REAL：表示值是一个浮点值，存储为8位IEEE浮点数。
> 
> text：表示值是一个文本字符串，使用数据库编码(utf-8，utf-16be或utf-16le)存储
> 
> BLOB：表示值是一个数据块，与输入的数据完全相同（人话说就是二进制数据，可以存储图片，音乐等）
![image](https://github.com/MRSlouzk/Nonebot-plugintutorials/blob/main/imgs/sqlite%E5%88%9B%E5%BB%BA%E8%A1%A82.png)

### 2-2创建对象（常说的表中的行）
模拟数据库网站：
#### 基础sql脚本(关系型数据库中基本通用)
> insert语句：向一个表中添加行，格式：insert into TABLE values (VALUE1,VALUE2,VALUE3,...VALUEN);  (如果表中有这个主键会报错哦，下面会教条件语句来防止)
> 
> select语句：在控制台中输出这个表中的所有行以及他的字段，格式：select * from TABLE_NAME;
> 
> drop语句：删除表的一个语句，格式：drop table TABLE_NAME;
> 
> create语句：创建表的语句，格式：create table TABLE_NAME(TYPE_1 NAME_1,TYPE 2 NAME_2.........);
>  
>  where语句：寻找一个表中符合条件的一项，例如我们要找一个主键ID为3的可以这么写select * from TABLE_NAME where ID=3; (注意sqlite中的等于是一个等于号)
> 
> delete语句：删除表中的一项，这时就可以用到where语句了，例如找到ID为3之后我们要删除它，可以这么写delete from TABLE_NAME where ID-3;
