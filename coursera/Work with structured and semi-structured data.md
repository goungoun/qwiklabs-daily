# Work with structured and semi-structured data
- Coursera: Data Engineering on Google Cloud Platform
- Levels: fundamental
- Link: https://googlecoursera.qwiklabs.com/catalog_lab/1363

# Summary
- master node에 접속하여 hdfs에 put 하고 Hadoop admin (9870)> Utility 메뉴에서 파일 확인
- Keywords: `9870` `Hive`

## VPC Network
- Name: allow-hadoop
- Direction of traffic: Ingress
- Action on match: Allow
- Targets: Specified on target tags
- Target tags: hadoopaccess
- Source IP range: <your-IP>/32
> What is 32? 
- Specified protocols and ports: 9870, 8088

## HDFS
~~~bash
hadoop fs -mkdir /pet-details
hadoop fs -put pet-details.txt /pet-details
hadoop fs -get /GroupedByType/part* .
~~~

## Hive
~~~sql
CREATE DATABASE pets;
USE pets;
CREATE TABLE details (Type String, Name String, Breed String, Color String, Weight Int) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',';
SHOW TABLES;
DESCRIBE pets.details;
load data INPATH '/pet-details/pet-details.txt' OVERWRITE INTO TABLE details;
SELECT * FROM pets.details;
~~~

## Pig
~~~sql
rmf /GroupedByType
x1 = load '/pet-details' using PigStorage(',') as (Type:chararray,Name:chararray,Breed:chararray,Color:chararray,Weight:int);
x2 = filter x1 by Type != 'Type';
x3 = foreach x2 generate Type, Name, Breed, Color, Weight, Weight / 2.24 as Kilos:float;
x4 = filter x3 by LOWER(Color) == 'black' or LOWER(Color) == 'white';
x5 = group x4 by Type;
store x5 into '/GroupedByType';
~~~

## Comment
- hdfs dfs 가 아니라 deprecated된 hadoop fs명령어로 lab이 작성되어있음
- master node에 바로 접속하여 실습하는 부분도 수정되었으면 좋겠음. 다른 vm에서는 hdfs에 접속할 수 없는 것인가?
- beeline은 실행 URL을 얻고 싶다
- csv가 과연 unstructured data일까?
