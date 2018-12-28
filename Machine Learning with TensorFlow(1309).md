# Machine Learning with TensorFlow
- Levels: advanced
- Permalink: https://google.qwiklabs.com/catalog_lab/1309

## Summary
- Linear classifier model 
- key words: `time-window average` `ml-engine`

## Compute Engine
- VM에 python 과 관련 모듈 설치 후 소스 실행

## Data Source
- Bureau of Transport Statistics
 (집계하는 방법은 Processing Time Windowed Data with Apache Beam and Cloud Dataflow 에서 배움)
- train 데이터 가져오기
~~~bash
$ gsutil cp \
  gs://${BUCKET}/flights/chapter8/output/trainFlights-00001*.csv \
  full.csv
$ head -10003 full.csv > ~/data/flights/train.csv
~~~
- 같은 방법으로 test 데이터는 testFlights-xxx의 것을 가져와서 cp

## 부족한 부분
- python package 만드는 법

## Trouble Shooting
- 일시적인 오류 발생 나중에 다시 시도
~~~
Lab failed on 2018-11-03 11:05:43 -0400: update_deployment_manager_info: [can't create Thread: Resource temporarily unavailable] Any credits or access code you used to start this lab have been refunded.
~~~

## Comment
- Google Storage를 다루는 커맨드인 `gsutil`은 하둡에서의 `hdfs dfs`, 혹은 리눅스의 파일시스템을 다루는 명령어와 유사함. cp, cat, du, rm, rsync, stat 등 사용 가능
- 같은 데이터를 train/test data로 쪼개는 것이 아니라 이미 output 디렉토리에 쪼개져있음

