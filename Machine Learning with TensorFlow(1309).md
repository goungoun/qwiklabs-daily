# Machine Learning with TensorFlow
- Levels: advanced
- Permalink: https://google.qwiklabs.com/catalog_lab/1309

## Summary
- 소스코드를 따라하는 부분이 쉽지 않고 들여쓰기에 오류가 있어서 소스코드 별도 보관
- Source: [model.py](./src/1309/model.py), [task.py](./src/1309/task.py) 파라미터 처리, __init__.py는 패키지를 만들기 위한 빈 파일
- Linear classifier model 
- key words: `ml-engine` `tensorflow` `PKG-INFO` `setup.cfg` `setup.py`

## Compute Engine
- cloud shell이 아니라 VM에서 python 과 관련 모듈과 `tensorflow`를 설치 후 소스 실행

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

## Trouble Shooting
- 일시적인 오류 발생 나중에 다시 시도
~~~
Lab failed on 2018-11-03 11:05:43 -0400: update_deployment_manager_info: [can't create Thread: Resource temporarily unavailable] Any credits or access code you used to start this lab have been refunded.
~~~
- TypeError 발생
~~~
$ python task.py \
 --traindata ~/data/flights/train.csv \
 --output_dir ./trained_model \
 --evaldata ~/data/flights/test.csv
Traceback (most recent call last):
  File "task.py", line 34, in <module>
    feats, label = model.read_dataset(traindata)
TypeError: 'function' object is not iterable
~~~
- no module 오류: `sudo pip install tensorflow`로 해결

## Comment
- Google Storage를 다루는 커맨드인 `gsutil`은 하둡에서의 `hdfs dfs`, 혹은 리눅스의 파일시스템을 다루는 명령어와 유사함. cp, cat, du, rm, rsync, stat 등 사용 가능
- 같은 데이터를 train/test data로 쪼개는 것이 아니라 이미 output 디렉토리에 쪼개져있음
- 빈 파일을 만들어서 코드를 조각조각 여러번 추가하는 방식으로 되어있어서 따라하기가 쉽지 않고 중간에 조금이라도 빼먹거나 잘못해서 발생하는 오류를 알아서 디버깅해야함

