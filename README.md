<img src="https://hadoop.apache.org/hadoop-logo.jpg" width="200">

# Big Data Single Instance
<i>Hadoop | Hive | Prestodb</i>

---

### วิธีการเรียกใช้งาน

```bash
cd /path/to/hadoop
git clone https://github.com/ezynook/docker-bigdata.git
cd docker-bigdata
docker-compose up -d
```
### Basic Command
```bash
docker exec -it hive /bin/bash
$ hive
{show databases, show tables}
```
### Create Database
```sql
CREATE DATABASE
    `testdb`
COMMENT
    'Default Hive database'
LOCATION
    '/user/hive/warehouse/testdb'
```
### Create Table
CSV Type
```sql
CREATE TABLE `testdb.testtbl`(                      
    `id` string,                                     
    `name` string)                               
ROW FORMAT SERDE                                   
    'org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe'  
WITH SERDEPROPERTIES (                             
    'field.delim'='|',                          
    'serialization.format'='|')                 
STORED AS INPUTFORMAT                              
    'org.apache.hadoop.mapred.TextInputFormat'       
OUTPUTFORMAT                                       
    'org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat' 
LOCATION                                           
    '/user/hive/warehouse/testdb/testtbl' 
TBLPROPERTIES (
    "skip.header.line.count"="1"
);
```
PARQUET Type
```sql
CREATE EXTERNAL TABLE `testdb.testtbl`(                         
    `id` int,                                        
    `name` string,                                   
    `othername` string)                              
ROW FORMAT SERDE                                   
    'org.apache.hadoop.hive.ql.io.parquet.serde.ParquetHiveSerDe'  
STORED AS INPUTFORMAT                              
    'org.apache.hadoop.hive.ql.io.parquet.MapredParquetInputFormat'  
OUTPUTFORMAT                                       
    'org.apache.hadoop.hive.ql.io.parquet.MapredParquetOutputFormat' 
LOCATION                                           
    '/user/hive/warehouse/testdb/testtbl'
---------------------OR--------------------
CREATE EXTERNAL TABLE `testdb.testtbl`(                         
    `id` int,                                        
    `name` string,                                   
    `othername` string)                               
STORED AS PARQUET
LOCATION                                           
    '/user/hive/warehouse/testdb/testtbl'
```
### Access to hive
```bash
docker exec -it hive /bin/bash -c "hive"
```
### Access HDFS Store
```bash
docker exec -it hive /bin/bash -c "hdfs dfs -ls /user/hive/warehouse/table_name"
```
### Csv file to HDFS
นำไฟล์ CSV ที่ได้ทำการ ETL หรือ Cleaned แล้ว นำไฟล์มาวางไว้ที่ /tmp (CSV Seperate ต้องเป็น PIPE "|" เท่านั้น) และถ้าหากมีหลายไฟล์ชื่อต้องห้ามซ้ำกัน จากนั้นทำการ Add to HDFS Store โดยใช้คำสั่ง
```bash
docker exec -it hive bash
hdfs dfs -put /tmp/csv_file_name.csv /user/hive/warehouse/testdb/testtbl/
```
จากนั้นลอง List ดูว่ามีไฟล์อยู่จริงๆหรือไม่
```bash
hdfs dfs -ls /user/hive/warehouse/testdb/testtbl/
```
ลอง Query โดยใช้ Hive ว่ามีข้อมูล rows หรือไม่
```bash
docker exec -it hive bash
$ hive
$ use testdb;
$ select * from testdb.testtbl limit 10;
```
จากนั้นดูว่ามีข้อมูลตามที่เราได้ทำ CSV ไว้หรือไม่ หรือจะใช้ Third-Party Software ได้จากลิ้งนี้ [Download DBeaver](https://dbeaver.io/download/)

---

> <i><b>Pasit Yodsoi</b> @Data Engineer</i>