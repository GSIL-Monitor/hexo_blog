[toc]

# Presto & Hive & Spark & Redshift

## 常见设置

```sql
set tez.runtime.io.sort.mb=1024;
set tez.job.name=dw_user_building_detail;
set mapred.reduce.tasks=40;
set hive.exec.dynamic.partition=true;
set hive.exec.dynamic.partition.mode=nonstrict;
```

## 基本操作

### 建立连接

```sql
-- hdfs
hdfs dfs -put /etc/test /tmp/
-- hive
beeline -u jdbc:hive2://
```

### 表结构

```sql
-- presto & hive & spark & mysql
desc formatted schemas.table_name;
show create table schemas.table_name;
show columns from schemas.table_name;
```

### 建表语句

```sql
--hive
CREATE TABLE schemas.table_name(
  `col1` bigint,
  `col2` timestamp,
  `col3` decimal(20,2)
)
PARTITIONED BY (
  `col4` int,
  `col5` string
) ROW FORMAT DELIMITED FIELDS TERMINATED BY '|'
 LOCATION '';

-- mysql
CREATE TABLE schemas.table_name (
  `col1` int(11) NOT NULL,
  `col2` tinyint(1) NOT NULL DEFAULT '0',
  `col3` varchar(256) NOT NULL,
  `col4` datetime DEFAULT '1970-01-01 00:00:00',
  `col5` text NOT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

-- redshift
CREATE TABLE schemas.table_name (
  `col1` int2 NOT NULL,
  `col2` int8 NOT NULL DEFAULT '0',
  `col3` varchar(256) NOT NULL,
  `col4` numeric(20,2) NOT NULL,
  `col5` date,
  `col6` timestamp
);
```

### 修改表结构

```sql
alter table schemas.table_name change col_name new_col_name decimal(38,4) CASCADE;
alter table schemas.table_name rename to schemas.new_name; -- 外部表不会修改loaction地址，内部表会修改映射地址
```

### 插入数据

```sql
-- redshift & mysql（直接插入，不会覆盖原始数据）
delete from schemas.table_name where ..;
insert into schemas.table_name
select ...;
commit;

-- hive（会覆盖分区的部分数据）
insert overwrite table schemas.table_name partition(col4,col5)
select ...
union all
select * from schemas.table_name
where ...;
```

## 查询语句

### 数据类型转换

- presto 中的进行比较的数据类型必须强制一致，常用 cast(col as XXX) 进行强制转换。
- 常见的数据类型
  - boolean：true|false
  - tinyint：2^7 - 1
  - smallint：2^15 - 1
  - int 或 integer：2^31 - 1
  - bigint：2^63 - 1
  - real：2^31 - 1
  - double：2^63 - 1
  - decimal(precision,scale)：最高可取(38,18)
  - varchar【presto & redshift】、string【hive & spark】
  - date：默认格式为 YYYY-MM-DD
  - timepstamp：默认格式为 YYYY-MM-DD HH:mm:ss
  - interval：常用单位为 second、minute、hour、day、week、month、quarter、year

### 判空处理

```sql
-- hive & spark & redshift
select nvl(0,0);
-- presto & hive & spark & redshift
select coalesce(0,0);
```

### 时间函数

- 时间加减
    ```sql
    -- hive & spark
    select date_add('2018-01-31',-30)           -- '2018-01-01'
        ,date_sub('2018-01-31',30)              -- '2018-01-01'
        ,datediff('2018-01-31','2018-01-21');   -- 10，前者 - 后者，只可以取到day

    -- presto
    select cast('2018-01-31' as timestamp) - interval '30' day                   -- '2018-01-01 00:00:00'
        ,date_diff('day',cast('2018-01-21' as date),cast('2018-01-31' as date)); -- 10，后者 - 前者，可精确到年月日时分秒

    -- redshift
    select cast('2018-01-31' as timestamp) - interval '30d'                      -- '2018-01-01 00:00:00'
        ,date_diff('day',cast('2018-01-21' as date),cast('2018-01-31' as date)); -- 10，后者 - 前者，可精确到年月日时分秒
    ```
- 获取指定格式的日期字符串
    ```sql
    -- hive & spark
    select date_format('2011-09-20 08:30:45', 'yyyy-MM-dd HH:00:00');

    -- presto
    select format_datetime(cast('2011-09-20 08:30:45' as timestamp),'yyyy-MM-dd HH:00:00');

    -- redshift
    select to_char(cast('2011-09-20 08:30:45' as timestamp),'yyyy-MM-dd HH:00:00');
    ```
- 当前时间
    ```sql
    -- hive & spark
    select current_timestamp  -- 当前时间的时间戳，默认为UTC时间
        ,current_date;                 -- 当前日期

    -- presto
    select current_timestamp           -- 当前时间的时间戳，默认为UTC8时间
        ,current_date;                 -- 当前日期

    -- redshift
    select current_timestamp + interval '8h'  -- 当前时间的时间戳，默认为UTC时间
        ,current_date;                        -- 当前日期
    ```
- unix 时间戳
    ```sql
    -- hive
    select unix_timestamp('20170208 11:15:50','YYYYMMDD HH:mm:ss');  -- 1486523750，默认为 YYYY-MM-DD HH:mm:ss，可不填
    select from_unixtime(unix_timestamp('2017-02-08 11:15:50') + 5,'yyyy-MM-dd HH:mm:ss');  -- '2017-02-08 11:15:55'

    -- presto
    select to_unixtime(cast('2017-02-08 11:15:50' as timestamp));  -- 1.48652375E9
    select from_unixtime(1486523750 + 5);  -- '2017-02-08 11:15:55'
    ```

- 获取周一、周日的日期

    ```sql
    -- hive
    -- 周一
    select date_add('2018-12-27',- pmod(datediff('2018-12-27','2000-01-03'),7));
    -- 周日
    select date_add('2018-12-27',6 - pmod(datediff('2018-12-27','2000-01-03'),7));

    -- redshift
    -- 周一
    select date_add('day',- mod(date_diff('day',cast('2000-01-03' as date),cast('2018-12-27' as date)),7),cast ('2018-12-27' as date));
    -- 周日
    select date_add('day',6 - mod(date_diff('day',cast('2000-01-03' as date),cast('2018-12-27' as date)),7),cast ('2018-12-27' as date));

    -- presto
    -- 周一
    select date_add('day',1-day_of_week(cast('2018-12-23' as date)),cast('2018-12-23' as date));
    -- 周日
    select date_add('day',7-day_of_week(cast('2018-12-23' as date)),cast('2018-12-23' as date));
    ```

### 字符串函数

- 替换
    ```sql
    -- hive & spark & redshift
    select translate('aabbcc','a','d');  -- 'ddbbcc'

    -- presto & redshift
    select replace('aabbcc','a','d');  -- 'ddbbcc'
    ```
- 查找
    ```sql
    -- hive & spark
    select instr('aabbcc', 'abc');   -- 0

    -- presto & redshift
    select strpos('aabbcc', 'abc');   -- 0
    ```
- 拼接
    ```sql
    -- hive & spark & mysql
    select concat_ws(',','aa','bb','cc')  -- 'aa,bb,cc'
        ,concat('aa','bb','cc');          -- 'aabbcc'

    -- presto
    select 'aa'||'bb'||'cc'               -- 'aabbcc'
        ,concat('aa','bb','cc');          -- 'aabbcc'

    -- redshift
    select 'aa'||'bb'||'cc';              -- 'aabbcc'
    ```
- 模式匹配：hive中的转义字符需要使用双斜杠，而presto中仅需要单斜杠即可
    ```sql
    -- hive & spark
    select regexp_extract('1a-2b-14b-3c', '\\d+b', 0);    -- 2b
    select regexp_replace('1a-2b-14b-3c', '\\d+b', '3c'); -- 1a-3c-3c-3c
    select '1a-2b-14b-3c' rlike '\\d+b';                  -- true
    select '1a-2b-14b-3c' like '\\d+b';                   -- false，spark中不可用
    select split('1a-2b-14b-3c','\\d+b');                 -- ["1a-","-","-3c"]

    -- presto
    select regexp_extract('1a-2b-14b-3c', '\d+b');         -- 2b
    select regexp_extract_all('1a-2b-14b-3c', '\d+b');     -- {"2b","14b"}
    select regexp_replace('1a-2b-14b-3c', '\d+b','3c');    -- 1a-3c-3c-3c
    select regexp_like('1a-2b-14b-3c', '\d+b');            -- true
    select regexp_split('1a-2b-14b-3c', '\d+b');           -- {"1a ","-","-","-3c"}

    --  redshift
    select '1a-2b-14b-3c' like '%[0-9]+b%';         -- false
    select '1a-2b-14b-3c' similar to '$[0-9]+b$';   -- true
    select 'abc' ~ '.*(b|d).*';                     -- true
    select split_part('aa/bb/cc/d','/',1);          -- 'aa'
    ```

- json解析
    ```sql
    -- hive
    select get_json_object(snapshot_value, '$.barrack_sub_a') from ods.user_daily where game_id=1012 and create_time = '2019-01' limit 10;

    -- presto
    select json_extract(snapshot_value, '$.barrack_sub_a') from ods.user_daily where game_id=1012 and create_time = '2019-01' limit 10;
    ```

### group by & having

- presto & spark & redshift 可以在 group by时使用数字代替指定位置的字段，hive不可以；
- presto 在使用 having 筛选时字段必须要一致，不能直接使用重命名的字段名，hive & spark & redshift 均可以。
    ```sql
    -- hive & spark & redshift
    select open_udid,count(1) as counts
    from dw.udid
    where game_id = 1031
    group by open_udid having counts > 1;

    -- presto & spark & redshift
    select open_udid,count(1) as counts
    from dw.udid
    where game_id = 1031
    group by 1 having count(1) > 1;
    ```

### 分位数函数 percentile_approx

```sql
-- hive & spark
SELECT play_date,
        count(DISTINCT user_id) AS fps_player_count,
        percentile_approx(fps_game_count, 0.5) AS fps_game_count_median,
        percentile_approx(fps_game_count, 0.25) AS fps_game_count_25_quantile,
        percentile_approx(fps_game_count, 0.75) AS fps_game_count_75_quantile
FROM fps_game_info1
GROUP BY play_date;

-- presto & redshift
SELECT A.play_date,
        count(DISTINCT A.user_id) AS fps_player_count,
        max(case when A.rank = cast(A.per_day_count * 0.5 as int) then A.fps_game_count else 0 end) as fps_game_count_median,
        max(case when A.rank = cast(A.per_day_count * 0.25 as int) then A.fps_game_count else 0 end) as fps_game_count_25_quantile,
        max(case when A.rank = cast(A.per_day_count * 0.75 as int) then A.fps_game_count else 0 end) as fps_game_count_75_quantile
FROM(
    select play_date
            ,fps_game_count
            ,user_id
            ,row_number() over(partition by play_date order by fps_game_count asc) as rank
            ,count(1) over(partition by play_date) per_day_count
    from fps_game_info1
) A
GROUP BY 1;
```

## 数据导入导出

### Redshift -> S3

```sql
unload (
'select * from dw_me.user_ip_most_often_vip_lxc')
to 's3://dw-muse-third/tmp/lxc9/'
credentials 'aws_access_key_id=AKIAICQLDGY7FGKBM6UQ;aws_secret_access_key=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'
DELIMITER AS ',' ALLOWOVERWRITE ESCAPE GZIP;
```

- s3AccessKey 、 s3SecretKey 、s3a:// 地址，可自行修改
- 部分字符需要转义，详情参考 [redshift unload 函数](https://docs.aws.amazon.com/zh_cn/redshift/latest/dg/r_UNLOAD.html "unload")
- 不可在外部查询中使用 limit，若需要使用 limit 请参照例子使用子查询

### Hive -> S3

```sql
-- 获取目标表的地址，location 在 HDFS，则需要新建表定位到s3
desc formatted table_name;

create table tmp.wizzo_user (
    user_group   string,
    user_id      varchar(32),
    user_no      bigint,
    install_time     timestamp
)
row format delimited fields terminated by ','
collection items terminated by '-'
map keys terminated by ':'
location 's3a://dw-muse-third/tmp/lxc5/';

insert into table tmp.wizzo_user
select
    user_group
    ,user_id
    ,user_no
    ,install_time
from ...
```

### HDFS|本地 <-> Hive

```sql
hadoop dfs -get hdfs://host:port/user/hadoop/file localfile

hadoop dfs -put localfile hdfs://host:port/user/hadoop/file

load data [local] inpath '/user/data/staging/page_view'
[overwrite] into table tmp.wizzo_user;
```

> Beeline不支持 load data local inpath 和 insert overwrite local directory

### S3 -> 本地 或 HDFS

参考文档： [s3cmd](https://s3tools.org/s3cmd "s3")

- 本地安装s3cmd ,    ```pip install s3cmd```
- 配置s3cmd
    1. 命令行输入  ```s3cmd --configure```
    2. 输入两个密钥(s3AccessKey 、s3SecretKey) 和时区 us-west-2
    3. 一直回车 或 y
- 查看文件 ```s3cmd ls s3://dw-muse-third/tmp/lxc6/```
- 下载指定文件至本地
    ```sql
    s3cmd get s3://dw-muse-third/tmp/lxc6/ /Users/liuxingchi/Downloads/msgdata
    ```

### Mysql -> 本地

1. mysqldump 命令
    ```sql
    mysqldump -h localhost -u root -p mydb mytable > target_file
    ```
2. 运行脚本
    ```sql
    SELECT * FROM tablename
    INTO OUTFILE 'target_file'
    [option];

    select * from tmp.user_info 
    into outfile '/tmp/emp.txt' 
    fields terminated by "," enclosed by '"';
    ```
其中 option 参数可以是以下选项：

命令参数 | 说明
---|---
fields terminated by '字符' |字段分隔符，默认字符为制表符'\t'
fields [optionally] enclosed by '单字符' |字段引用符，加上optionally后在数字类型上不会有引用符号
fields escaped by '单字符' |转义字符，默认为'\'
lines starting by '字符' |每行前都加此支付，默认为空
lines terminated by '字符' |行结束符，默认为'\n'

### Presto -> 本地

1. 下载 [presto-cli-xxx-executable.jar](https://prestodb.io/docs/current/installation/cli.html "presto")
2. 重命名为 presto，执行./presto --help，会有相关可利用选择
3. 执行以下脚本
    ```cmd
    presto --server ip:port --catalog=xxx --user=xxx --execute '需要导出的sql查询命令' --output-format CSV > xxx.csv
    ```
4. 如果csv出现中文乱码，执行下面指令：
    ```cmd
    iconv -f UTF-8 -t GB18030 xxx.csv >tmp.csv
    ```