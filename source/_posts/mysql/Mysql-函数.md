---
Title: Mysql 函数
---



## 字符串函数

### substring_index(str,delim,count)

返回str中第 count次出现的分隔符delim之前的子字符串。

count为负数则相反。

```
select substring_index('1,2,3,4,5,6,7',',',1); // 1
select substring_index('1,2,3,4,5,6,7',',',3); // 1,2,3
select substring_index('1,2,3,4,5,6,7',',',-1); // 7
select substring_index('1,2,3,4,5,6,7',',',-3); // 5,6,7
```

##### 小技巧:

```
如果取出一个字串中的第n个，可以使用嵌套的方式：substring_index(substring_index(str,delim,n),-1) 
```

### SUBSTRING

#### SUBSTRING(str,pos)、SUBSTRING(str FROM pos)

不带len的参数返回字符串中pos后的全部字串

```
select substring('abcdefg',5);     // efg
select substring('abcdefg' from 5);// efg
```

#### SUBSTRING(str,pos,len)、SUBSTRING(str FROM pos FOR len)

len代表从pos向后取多少个字符

```
select substring('abcdefg',5,2);		// ef
select substring('abcdefg' from 5 for 2)// ef
```

### CHAR(N,...[USING charset_name])

会将每一个参数N都解释为整数，返回有这些整数在ASCII码中所对应字符所组成的字符串，忽略NULL值

```
select char(77,121,83,81,76); // MySQL
```

### CHAR_LENGTH(str)&CHARACTER_LENGTH(str)

单纯计算字符串长度，多字节字符也会被当成单字节对待。例如汉字

```
select CHAR_LENGTH('123') // 3
select CHAR_LENGTH('二磊') // 2
```

### CONCAT(str1,str2,....)

将多个字符串链接；

```
select CONCAT('1','2','345') // 12345
```

### CONCAT_WS(separator,str1,str2,...)

可以指定连接符的CONCAT

```
select CONCAT_WS('-','1','2','345') // 1-2-345
```

### CONV(N,from_base,to_base)

```
将数值在不同进制间转换。将数值型参数 N 由初始进制 from_base 转换为目标进制 to_base 的形式并返回。如果任何参数为 NULL，则返回 NULL。N 可以是整数，但也可以是字符串。进制取值范围为2-36。如果 to_base 为负值，N 被认为是有符号数值；反之，N被认为是无符号数值。函数运算精度为64位。
```

### ELT(N,str1,str2,str3,...)

```
如果 N = 1，则返回 str1，如果N = 2 则返回 str2，以此类推。如果 N 小于1或大于参数个数，则返回 NULL。ELT() 是 FIELD() 的功能补充函数。
```

### FIELD(str,str1,str2,str3,...)

```
返回 str 在后面的参数列（str1、str2、str3……）中的索引（起始索引为1）。如果未在参数列中发现 str 则返回0。
```

### FIND_IN_SET(str,strlist)

```
如果字符串 str 在由 N 个子字符串组成的字符串列表 strlist 中，则返回其在 strlist 中的索引（字符串列表 strlist 的初始索引为1）。
select FIND_IN_SET('b','b,a,b,a,g'); // 1
select FIND_IN_SET('a','b,a,b,a,g'); // 2
select FIND_IN_SET('d',',b,a,b,a,g,'); // 0
select FIND_IN_SET('a',',b,a,b,a,g,'); // 3
```

### FORMAT(X,D)

```
将数值参数 `X` 以'#,###,###.##' 的形式进行格式化，并四舍五入到小数点后 `D` 位处，然后将格式化结果以字符串形式返回。如果 `D` 为0，则结果没有小数部分。示例如下：
select FORMAT(123456.78901,3) // 123,456.789
```

### HEX(N_or_S)

```
当 `N_or_S` 为数值时，以16进制数的字符串形式返回 `N` 的值，`N` 为 BIGINT 型值。该函数作用等同于 `CONV(N, 10, 16)`。

当 `N_or_S` 为字符串时，返回 `N_or_S` 的16进制字符串形式，`N_or_S` 中的每个字符都被转换为2个16进制数字。示例如下：
select HEX(255); // FF
select HEX('abc'); // 616263
```

### INSERT(str,pos,len,newstr)

```
    在原始字符串str中，将自作数第pos位开始，长度为len个字符的字符串替换为newstr。
	如果pos不在原始字符串长度内，则返回原始字符串，pos从1开始。
	如果len不在原始字符串长度内，则返回原始字符串中自pos位起的所有字符串。

	select INSERT('123456',1,3,'543') //543456 
```

### INSTR(str,substr)

```
返回 substr 在 str 中第一次出现时的位置（也就是索引）。作用类似于双参数版本的 LOCATE() 函数，只不过参数的次序调换了过来。示例如下：
SELECT INSTR('foobarbar', 'bar'); // 4
下标从1开始
```

### LEFT(str,len)

```
select left('123456',3); // 123
```

### LENGTH(str)

```
返回字符串str的字节长度，多字节字符被如实计算为多字节。
select length('123456'); -- 6
select length('二磊')  -- 6
```

### LOCATE(substr,str), LOCATE(substr,str,pos)

```
第一种格式函数的作用如下：返回 substr 在 str 中第一次出现的位置（即索引）。第二种格式函数则返回自 str 指定位置 pos （即索引）起， substr 在 str 中第一次出现的位置。如果在 str 中未找到 substr，则两种函数都返回0。示例如下：
SELECT LOCATE('bar', 'foobarbar'); -- 4
SELECT LOCATE('bar', 'foobarbar',5); -- 4
```

### LPAD(str,len,padstr)

```
左补齐函数。将字符串 str 左侧利用字符串 padstr 补齐为整体长度为 len 的字符串。如果 str 大于 len，则返回值会缩减到 len 个字符。示例如下：

select LPAD('abc',4,'123') -- 1abc
```

### LTRIM(str)

### MAKE_SET(bits,str1,str2,...)

```
返回一个集合值（是一个由字符 `,` 所分隔的众多子字符串所组合而成的字符串），该集合中包含的字符串的比特数等于`bits` 集合中的对应比特数。例如，`str1` 对应着 `bit 0`，`str2` 对应 `bit 1`，以此类推。`str1`、`str2`……中的 NULL 值将不会添加到结果中。示例如下：
SELECT MAKE_SET(2,'c','b','c'); -- b
```

### MID(str,pos,len)

`MID(str,pos,len)` 作用等同于 `SUBSTRING(str,pos,len)`。

### POSITION(substr IN str)

作用等同于 `LOCATE(substr,str)`。

### expr REGEXP pattern

将 `expr` 与 `pattern` 进行模式匹配。如果相匹配则返回1，否则返回0。如果 `expr` 与 `pattern` 两个参数中有一个为 NULL，则结果也为 NULL。除了在处理二进制字符串时，REGEXP 对大小写并不敏感。第1个示例如下：

```
mysql> SELECT 'ABCDEF' REGEXP 'A%C%%';
+---------------------------------------------------------+
| 'ABCDEF' REGEXP 'A%C%%'                                 |
+---------------------------------------------------------+
| 0                                                       |
+---------------------------------------------------------+
1 row in set (0.00 sec)
```

第2个实例：

```
mysql> SELECT 'ABCDE' REGEXP '.*';
+---------------------------------------------------------+
|  'ABCDE' REGEXP '.*'                                    |
+---------------------------------------------------------+
| 1                                                       |
+---------------------------------------------------------+
1 row in set (0.00 sec)
```

第3个示例：

```
mysql> SELECT 'new*\n*line' REGEXP 'new\\*.\\*line';
+---------------------------------------------------------+
| 'new*\n*line' REGEXP 'new\\*.\\*line'                   |
+---------------------------------------------------------+
| 1                                                       |
+---------------------------------------------------------+
1 row in set (0.00 sec)
```

### REPEAT(str,count)

将字符串`str`重复`count`次，返回结果字符串，如果count小于1，则返回一个空字符串，如果`str`或`count`为NULL，则返回NULL。示例如下：

```
mysql> SELECT REPEAT('MySQL', 3);
+---------------------------------------------------------+
| REPEAT('MySQL', 3)                                      |
+---------------------------------------------------------+
| MySQLMySQLMySQL                                         |
+---------------------------------------------------------+
1 row in set (0.00 sec)
```

### REPLACE(str,from_str,to_str)

查找字符串 `str` 中出现的 `from_str` ，将其都更换为 `to_str`。在查找 `from_str` 时，函数对大小写是敏感的。示例如下：

```
mysql> SELECT REPLACE('www.mysql.com', 'w', 'Ww');
+---------------------------------------------------------+
| REPLACE('www.mysql.com', 'w', 'Ww')                     |
+---------------------------------------------------------+
| WwWwWw.mysql.com                                        |
+---------------------------------------------------------+
1 row in set (0.00 sec)
```

### REVERSE(str)

```
mysql> SELECT REVERSE('abcd');
+---------------------------------------------------------+
| REVERSE('abcd')                                         |
+---------------------------------------------------------+
| dcba                                                    |
+---------------------------------------------------------+
1 row in set (0.00 sec)
```

### RIGHT(str,len)

### RPAD(str,len,padstr)

类似LPAD

### SPACE(N)

返回N个空格字符串

### STRCMP(str1, str2)

对比两个字符串 `str1` 和 `str2`，如果两字符串相等，返回1；如果当前的排序规则，`str1` 小于 `str2`，则返回-1，反之则都返回1。第1个示例如下：

```
mysql> SELECT STRCMP('MOHD', 'MOHD');
+---------------------------------------------------------+
| STRCMP('MOHD', 'MOHD')                                  |
+---------------------------------------------------------+
| 0                                                       |
+---------------------------------------------------------+
1 row in set (0.00 sec)
```

第2个示例如下：

```
mysql> SELECT STRCMP('AMOHD', 'MOHD');
+---------------------------------------------------------+
| STRCMP('AMOHD', 'MOHD')                                 |
+---------------------------------------------------------+
| -1                                                      |
+---------------------------------------------------------+
1 row in set (0.00 sec)
```

第3个示例如下：

```
mysql> SELECT STRCMP('MOHD', 'AMOHD');
+---------------------------------------------------------+
| STRCMP('MOHD', 'AMOHD')                                 |
+---------------------------------------------------------+
| 1                                                       |
+---------------------------------------------------------+
1 row in set (0.00 sec)
```



### ASCII(str)

```
返回字符串 str 中最左边字符的 ASCII 代码值。如果该字符串为空字符串，则返回0。如果字符串为 NULL 则返回 NULL。因为ASCII码表能表示的字符为256个，所以ASCII() 返回值在0-255之间。
```

### BIN(N)

```
返回十进制数值 N 的二进制数值的字符串表现形式。其中，N 是一 BIGINT 型数值。该函数等同于 CONV(N, 10, 2)。如果 N 为 NULL，则返回 NULL。示例如下：
```

### BIT_LENGTH(str)

```
返回字符串 str 所占的位长度。
```



## 最大最小函数

```
获取多列最大最小值
SELECT GREATEST(1,2,3,4,5,6); // 结果: 4
SELECT LEAST(1,2,3,4) // 结果: 1
获取一列中最大最小值
SELECT MAX(col1);  
SELECT MIN(col1); 
```

## 日期函数

### ADDDATE(date,INTERVAL expr unit),ADDDATE(expr,days)

在第二个参数使用`INTERVAL`格式时`ADDDATE()`作用就相当于`DATE_ADD()`。相关的函数`SUBDATE()`相当于`DATE_SUB()`

```
mysql> select ADDDATE(now(),INTERVAL 11 day) \
    -> union all \
    -> select now();
+--------------------------------+
| ADDDATE(now(),INTERVAL 11 day) |
+--------------------------------+
| 2021-01-01 19:44:44            |
| 2020-12-21 19:44:44            |
+--------------------------------+
mysql> select now() \
    -> union all \
    -> select DATE_ADD(now(),INTERVAL 11 day);
+---------------------+
| now()               |
+---------------------+
| 2020-12-21 19:45:51 |
| 2021-01-01 19:45:51 |
+---------------------+
```

当函数的第2个参数采用`days`格式时，MySQL会认为它是一个表示天数的整数，将它添加到`expr`上。示例如下：

```
mysql> select now() \
    -> union all \
    -> select ADDDATE(now(),11);
+---------------------+
| now()               |
+---------------------+
| 2020-12-21 19:49:34 |
| 2021-01-01 19:49:34 |
+---------------------+
```

### ADDTIME(expr1,expr2)

`ADDTIME()`将`expr2`参数加到`expr1`上。expr1是一个时间或日期时间表达式。`expr2`是一个时间表达式。

```
mysql> select now() \
    -> union all \
    -> select ADDTIME(now(),'10 1:1:1.000001');
+----------------------------+
| now()                      |
+----------------------------+
| 2020-12-21 19:55:28.000000 |
| 2020-12-31 20:56:29.000001 |
+----------------------------+
```

### CONVERT_TZ(dt,from_tz,to_tz)

```
mysql> SELECT CONVERT_TZ('2004-01-01 12:00:00','GMT','MET');
+-----------------------------------------------+
| CONVERT_TZ('2004-01-01 12:00:00','GMT','MET') |
+-----------------------------------------------+
| 2004-01-01 13:00:00                           |
+-----------------------------------------------+
mysql> SELECT CONVERT_TZ('2004-01-01 12:00:00','+00:00','+10:00');
+-----------------------------------------------------+
| CONVERT_TZ('2004-01-01 12:00:00','+00:00','+10:00') |
+-----------------------------------------------------+
| 2004-01-01 22:00:00                                 |
+-----------------------------------------------------+
```

### CURDATE()

返回当前日期函数，根据函数究竟用于字符串还是数字上下文，选择使用'YYYY-MM-DD' 或YYYYMMDD格式

```
mysql> select CURDATE();
+------------+
| CURDATE()  |
+------------+
| 2020-12-21 |
+------------+
1 row in set (0.01 sec)

mysql> select CURDATE() + 0;
+---------------+
| CURDATE() + 0 |
+---------------+
|      20201221 |
+---------------+
1 row in set (0.06 sec)
CURRENT_DATE 和 CURRENT_DATE() 实际上等于 CURDATE()。
```

### CURTIME()

根据函数究竟用于字符串或数字上下文，选择以'HH:MM:SS'还是HHMMSS格式。

```
mysql> select CURTIME();
+-----------+
| CURTIME() |
+-----------+
| 20:02:53  |
+-----------+
1 row in set (0.00 sec)

mysql> select CURTIME() + 0;
+---------------+
| CURTIME() + 0 |
+---------------+
|        200301 |
+---------------+
1 row in set (0.00 sec)

CURRENT_TIME 和 CURRENT_TIME() 都相当于 CURTIME()。
```

### CURRENT_TIMESTAMP 和 CURRENT_TIMESTAMP()

`CURRENT_TIMESTAMP` 和 `CURRENT_TIMESTAMP()` 实际上相当于 `NOW()`。

### DATE(expr)

```
mysql> select DATE('2020-12-21 01:01:30');
+-----------------------------+
| DATE('2020-12-21 01:01:30') |
+-----------------------------+
| 2020-12-21                  |
+-----------------------------+
1 row in set (0.01 sec)
```

### DATEDIFF(expr1,expr2)

`DATEDIFF()`将返回`expr1 - expr2`的值，用来表示两个日期相差的天数。`expr1` 和 `expr2` 都是日期或日期时间表达式。运算中只用到了这些值的日期部分。

```
mysql> select DATEDIFF('2020-12-21 14:00:00','2020-12-24');
+----------------------------------------------+
| DATEDIFF('2020-12-21 14:00:00','2020-12-24') |
+----------------------------------------------+
|                                           -3 |
+----------------------------------------------+
```

### DATE_ADD(date,INTERVAL expr unit) 与 DATE_SUB(date,INTERVAL expr unit)

执行日期计算的两种函数。`date`是一个用来指定开始日期的DATETIME或DATE值。`expr`是一种以字符串形式呈现的表达式，用来指定从开始日期增加或减少的间隔值。如果是负的间隔值，则`expr` 值的第一个字符是-号。unit是一个单位关键字，用来指定expr表达式应该采取的单位。

INTERVAL 关键字与单位说明符都不区分大小写。

| 单位所能取的值       | 期望的expr格式  |
| :------------------- | :-------------- |
| `MICROSECOND`        | 毫秒            |
| `SECOND`             | 秒              |
| `MINUTE`             | 分              |
| `HOUR`               | 小时            |
| `DAY`                | 日              |
| `WEEK`               | 周              |
| `MONTH`              | 月              |
| `QUARTER`            | 季度            |
| `YEAR`               | 年              |
| `SECOND_MICROSECOND` | '秒.毫秒'       |
| `MINUTE_MICROSECOND` | '分.毫秒'       |
| `MINUTE_SECOND`      | '分:秒'         |
| `HOUR_MICROSECOND`   | '小时.毫秒'     |
| `HOUR_SECOND`        | '小时:分:秒'    |
| `HOUR_MINUTE`        | '小时:分'       |
| `DAY_MICROSECOND`    | '日.毫秒'       |
| `DAY_SECOND`         | '日 小时:分:秒' |
| `DAY_MINUTE`         | '日 小时:分'    |
| `DAY_HOUR`           | '日 小时'       |
| `YEAR_MONTH`         |                 |

DATE_FORMAT(date,format)

该函数会根据`format`字符串来格式化date值。

| 格式标识符    | 说明                                                         |
| :------------ | :----------------------------------------------------------- |
| `%a`          | 一星期中每天名称的缩写（Sun...Sat）                          |
| `%b`          | 月份的缩写（Jan...Dec）                                      |
| `%c`          | 月份的数字表现形式（0...12）                                 |
| `%D`          | 带有英语后缀的一个月中的每一天的名称（0th、1st、2nd、3rd）   |
| `%d`          | 用数字形式表现的每月中的每一天（00...31）                    |
| `%e`          | 用数字形式表现的每月中的每一天（0...31）                     |
| `%f`          | 毫秒（000000...999999）                                      |
| `%H`          | 24时制显示的小时（00...23）                                  |
| `%h`          | 12时制显示的小时（01...12）                                  |
| `%I`          | 12时制显示的小时（01...12）                                  |
| `%i`          | 以数字形式表现的分钟数（00...59）                            |
| `%j`          | 一年中的每一天（001...366）                                  |
| `%k`          | 24时制小时的另一种表现格式（0...23）                         |
| `%l`          | 12时制小时的另一种表现格式（1...12）                         |
| `%M`          | 用完整英文名称表示的月份（January...December）               |
| `%m`          | 用数字表现的月份（00...12）                                  |
| `%p`          | 上午（AM）或下午（PM）                                       |
| `%r`          | 12时制的时间值（hh:mm:ss，后跟 AM 或 PM）                    |
| `%S`          | 秒（00...59）                                                |
| `%s`          | 秒（00...59）                                                |
| `%T`          | 24时制的小时（hh:mm:ss）                                     |
| `%U`          | 星期（00...53），其中星期天是每星期的开始日                  |
| `%u`          | 星期（00...53），其中星期一是每星期的开始日                  |
| `%V`          | 星期（01...53），其中星期天是每星期的开始日，和 `%X` 一起使用 |
| `%v`          | 星期（01...53），其中星期一是每星期的开始日，和 `%x` 一起使用 |
| `%W`          | 一星期中各日名称（Sunday...Saturday）                        |
| `%w`          | 一星期中各日名称（0代表星期日，6代表星期六，以此类推）       |
| `%X`          | 某星期所处年份。其中，星期天是每星期的开始日，采用4位数字形式表现，和 `%V`一起使用 |
| `%x`          | 某星期所处年份。其中，星期一是每星期的开始日，采用4位数字形式表现，和 `%V` 一起使用 |
| `%Y`          | 4位数字表示的年份                                            |
| `%y`          | 2位数字表示的年份                                            |
| `%%`          | 符号`%`的字面值                                              |
| %x（x为斜体） | 字符x的字面值，x指以上未列出的任何字符                       |

```
mysql> select DATE_FORMAT('20201212111111','%Y-%m-%d %H:%i:%S');
+---------------------------------------------------+
| DATE_FORMAT('20201212111111','%Y-%m-%d %H:%i:%S') |
+---------------------------------------------------+
| 2020-12-12 11:11:11                               |
+---------------------------------------------------+
mysql> select DATE_FORMAT('20201212111111','%Y-%m-%d %p %H:%i:%S');
+------------------------------------------------------+
| DATE_FORMAT('20201212111111','%Y-%m-%d %p %H:%i:%S') |
+------------------------------------------------------+
| 2020-12-12 AM 11:11:11                               |
+------------------------------------------------------+
```

### DAY()

`DAY()` 等同于`DAYOFMONTH()`

```
mysql> SELECT DAY(now());
+------------+
| DAY(now()) |
+------------+
|         21 |
+------------+
mysql> SELECT DAYOFMONTH(now());
+-------------------+
| DAYOFMONTH(now()) |
+-------------------+
|                21 |
+-------------------+
```

### DAYOFMONTH(date)

返回`date`参数所对应的一月中的第几天，取值范围从0到31。

### DAYOFWEED(date)

返回`date`参数所对应的每周中的每一天的索引值（1=Sunday，2=Monday，......7=Saturday）。这些索引值对应着ODBC标准。

```
mysql> SELECT now() \
    -> union all
    -> SELECT DAYOFWEEK(now());
+---------------------+
| now()               |
+---------------------+
| 2020-12-21 20:29:07 |
| 2                   |
```

### DAYOFYEAR(date)

```
mysql> SELECT NOW() \
    -> UNION ALL \
    -> SELECT DAYOFYEAR(NOW());
+---------------------+
| NOW()               |
+---------------------+
| 2020-12-21 20:30:05 |
| 356                 |
+---------------------+
```

### EXTRACT(unit FROM date)

`EXTRACT()` 函数使用同样的单位标识符`DATE_ADD()`或`DATE_SUB()`，但是只从date中提取相应部分，而不执行日期运算。

```
mysql> SELECT EXTRACT(YEAR FROM '2020-12-12')
    -> ;
+---------------------------------+
| EXTRACT(YEAR FROM '2020-12-12') |
+---------------------------------+
|                            2020 |
+---------------------------------+
```

### FROM_DAYS(N)

给定某日`N`,返回一个DATE值。

```
mysql> SELECT FROM_DAYS(738146);
+-------------------+
| FROM_DAYS(738146) |
+-------------------+
| 2020-12-22        |
+-------------------+
```

### FROM_UNIXTIME(unix_timestamp) FROM_UNIXTIME(unix_timestamp,format)

```
mysql> SELECT UNIX_TIMESTAMP() \
    -> UNION ALL \
    -> SELECT FROM_UNIXTIME(UNIX_TIMESTAMP());
+---------------------+
| UNIX_TIMESTAMP()    |
+---------------------+
| 1608583309          |
| 2020-12-21 20:41:49 |
+---------------------+
```

### HOUR(time)

```
mysql> SELECT HOUR('2020-12-22 14:15:16');
+-----------------------------+
| HOUR('2020-12-22 14:15:16') |
+-----------------------------+
|                          14 |
+-----------------------------+
```

### LAST_DAY(date)

```
mysql> SELECT NOW() \
    -> UNION ALL \
    -> SELECT LAST_DAY(NOW());
+---------------------+
| NOW()               |
+---------------------+
| 2020-12-21 20:43:36 |
| 2020-12-31 00:00:00 |
+---------------------+
```

### LOCALTIME 和 LOCALTIME()

`LOCALTIME` 和 `LOCALTIME()` 与 `NOW()` 具有相同意义。

### LOCALTIMESTAMP 和 LOCALTIMESTAMP()

`LOCALTIMESTAMP` 和 `LOCALTIMESTAMP()` 与 `NOW()`具有相同意义。

### MAKEDATE(year,dayofyear)

基于给定参数年份（`year`）和一年中的某一天（`dayofyear`），返回一个日期值。`dayofyear`必须大于0，否则结果为空。

`dayofyear`与[`DAYOFYEAR()`](https://wiki.jikexueyuan.com/project/mysql/useful-functions/time-functions.html#DAYOFYEAR)函数取值类似，取值范围为1-366。

```
mysql> SELECT MAKEDATE(2020,356);
+--------------------+
| MAKEDATE(2020,356) |
+--------------------+
| 2020-12-21         |
+--------------------+
```

### MAKETIME(hour,minute,second)

```
mysql> SELECT MAKETIME(12,13,14);
+--------------------+
| MAKETIME(12,13,14) |
+--------------------+
| 12:13:14           |
+--------------------+
```

### MICROSECOND

```
mysql> SELECT MICROSECOND('12:13:14.123456');
+--------------------------------+
| MICROSECOND('12:13:14.123456') |
+--------------------------------+
|                         123456 |
+--------------------------------+
```

### MINUTE()

```
mysql> SELECT MINUTE(now());
+---------------+
| MINUTE(now()) |
+---------------+
|            48 |
+---------------+
```

### MONTH

```
mysql> SELECT MONTH(now());
+--------------+
| MONTH(now()) |
+--------------+
|           12 |
+--------------+
```

### MONTHNAME(date)

基于`date`参数，返回月份的完整英文名称。

```
mysql> SELECT MONTHNAME('2020-12-13');
+-------------------------+
| MONTHNAME('2020-12-13') |
+-------------------------+
| December                |
+-------------------------+
```

### NOW()

```
mysql> SELECT NOW() \
    -> UNION ALL \
    -> SELECT NOW() + 1;
+---------------------+
| NOW()               |
+---------------------+
| 2020-12-21 20:51:39 |
| 20201221205140      |
+---------------------+
```

### PERIOD_ADD(P,N)

将N个月添加到时间段P（格式为YYMM或YYYYMM）上，返回格式为YYYYMM。注意：时段参数P不是一个日期值

```
mysql> SELECT PERIOD_ADD('7012',5);
+----------------------+
| PERIOD_ADD('7012',5) |
+----------------------+
|               197105 |
+----------------------+
1 row in set (0.00 sec)
```

### PERIOD_DIFF(P1,P2)

时段P1和P2之间的月份差值。P1与P2的格式应为YYYYMM或YYMM。

注意,时段参数`P1`和`P2`都不是日期值。

```
mysql> SELECT PERIOD_DIFF(9802,199703);
+--------------------------+
| PERIOD_DIFF(9802,199703) |
+--------------------------+
|                       11 |
+--------------------------+
```

