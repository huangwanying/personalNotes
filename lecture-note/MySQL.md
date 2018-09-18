# MySQL

## 增删改查简单操作

### insert

+ insert into 表名 (列名1,列名2...) values (值1,值2...);

###　update

+ update 表名 set 列1=新值1,列2=新值2 where expr;

### delete

+ delete from 表名 where expr;

### select

+ select 列1,列2...列n from 表名 where expr;
+ select 列名 as 别名, avg(列名) as 平均数别名 from result group by 列名 having （条件，如别名>2）

### order by 排序

+ 降序 desc
+ 升序（默认） asc
+ 多列排序：逗号隔开，每列按需求升序或者降序
+ select goods_id,cat_id,goods_name,shop_price from goods order by cat_id asc,shop_price desc;

### limit

+ limit 偏移量,取出条目的数目
+ select goods_id,cat_id,goods_name,shop_price from goods order by cat_id asc,shop_price desc limit 0,3;取前三个

### 子句的查询陷阱

+ where,group by,having,orderby,limit

