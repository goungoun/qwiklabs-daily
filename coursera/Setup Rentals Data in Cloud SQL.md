# Setup Rentals Data in Cloud SQL
- Levels: fundamental
- Link: https://googlecoursera.qwiklabs.com/focuses/16383

## Summary
- MySQL을 만들고 import 옵션을 다르게 설정하여 한번은 hql을 한번은 data를 import 하는 예제
- Keywords: `cloud_sql_proxy` `import`

## Cheat Sheet
~~~bash
cp cloudsql/* gs://qwiklabs-gcp-6b90230d50583f3f/sql
use recommendation_spark;
show tables;
./cloud_sql_proxy -instances=<INSTANCE_CONNECTION_NAME>=tcp:3306 & # public 탭에 instance connection name이 있음
mysql -u root -p --host 127.0.0.1
~~~

## mysql CLI
~~~bash
wget https://dl.google.com/cloudsql/cloud_sql_proxy.linux.amd64 -O cloud_sql_proxy 
chmod +x cloud_sql_proxy
./cloud_sql_proxy -instances=<INSTANCE_CONNECTION_NAME>=tcp:3306 & # 127.0.0.1로 mysql 접속
mysql -u root -p --host 127.0.0.1
~~~

## Comment
- 테이블을 생성하는 것도 import에서 작업 (선택옵션: SQL/CSV)
- local이나 cloud shell의 파일은 import할 수 없고 bucket에 한번 파일을 올려줘야 UI에서 작업가능 
- import 버튼이 잘 안 보임. import와 export는 화면 상단쪽에 따로 배치되어있음
- lab 기본 설정으로 설치 후에 서버를 업그레이드 할 수 있는 것인가?
- 어떻게 mysql host가 127.0.0.1로 접속이 되는거지요? cloud proxy 때문인가요?
