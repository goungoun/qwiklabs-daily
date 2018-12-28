## BigQuery: Qwik Start - Command Line
- Levels: introductory
- Link: https://google.qwiklabs.com/catalog_lab/684

## Summary
- 해당 프로젝트에 dataset.table을 만들고 csv를 입력하는 예제
- bq command
~~~bash
bq mk babynames
bq ls 
bq ls bigquery-public-data: # 다른 project에 접근할 때 : 사용
# wget http://www.ssa.gov/OACT/babynames/names.zip
# unzip names.zip
bq load babynames.names2010 yob2010.txt name:string,gender:string,count:integer
bq show babynames.names2010
bq query "select * from babynames.names2010"
~~~
> 파티션 테이블을 만들어서 년도별 파일을 입력하는 방법은?

## BigQuery Public Dataset
- `bq show bigquery-public-data:`
~~~
 baseball
 bitcoin_blockchain
 chicago_crime
 chicago_taxi_trips
 ..
 google_analytics_sample
 ..
 fda_drug
 fda_food
 ..
 new_york_citibike
 new_york_mv_collisions
 new_york_taxi_trips
~~~
-  `bq show`
~~~bash
$ bq show bigquery-public-data:samples.shakespeare
~~~
~~~bash
Table bigquery-public-data:samples.shakespeare

   Last modified                  Schema                 Total Rows   Total Bytes   Expiration   Time Partitioning   Labels
 ----------------- ------------------------------------ ------------ ------------- ------------ ------------------- --------
  15 Mar 02:16:45   |- word: string (required)           164656       6432064
                    |- word_count: integer (required)
                    |- corpus: string (required)
                    |- corpus_date: integer (required)
~~~
> 스키마를 보는 명령어에서 total rows가 표시되는데 항상 정확한 것일까? 어떻게 항상 정확한 통계를 유지할 수 있나?  

## Test
- You can access BigQuery using: Command line tool(bq), Web UI, BigQuery REST API
- Which CLI tool is use to interact with BigQuery service? bq

## Comment
- 빅쿼리의 dataset은 테이블 여러개를 묶어놓은 역할. Spark의 dataset과 개념이 많이 다름. 
- 쿼리를 실행할 때마다 shell로 로그인/로그아웃하는 것을 반복하지 않아도 됨
~~~
$ bq query "#standardSql
SELECT word, corpus, COUNT(word) FROM \`bigquery-public-data.samples.shakespeare\`
WHERE word LIKE '%huzzah%' GROUP BY word, corpus"
~~~
> 위 쿼리는 row를 반환하지 않는 쿼리인데 row 수를 결과로 표시하지 않기 때문에 당황 
~~~
bq query "#standardSql
SELECT word, corpus, COUNT(word) FROM \`bigquery-public-data.samples.shakespeare\`
WHERE word LIKE '%raisin%' GROUP BY word, corpus"
~~~
> 결과를 가져오는 쿼리에서도 역시 row 수는 반환이 안됨. 
> `bigquery-public-data:samples.shakespeare` 와 같은 표현 방식에서 project를 구분할 때 :을 사용하기 때문에 매번 `로 묶어줘야 하는 부분이 정말 불편한데 sql 안에서 쓰려면 역 슬래시로 한번 더 감싸줘야 해서 더 불편함. syntax를 설계할 때 :를 넣은 것은 정말 잘못한것 같음.