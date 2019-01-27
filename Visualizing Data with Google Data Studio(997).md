# Visualizing Data with Google Data Studio
- Levels: advanced
- Link: https://www.qwiklabs.com/catalog_lab/997

## mysql
- mysql ip 얻기
~~~bash
gcloud sql instances describe flights
MYSQLIP=$(gcloud sql instances describe flights --format="value(ipAddresses.ipAddress)")
~~~
- mysql
~~~sql
use bts;
describe flights;
select count(*) from flights;
CREATE VIEW delayed_10 AS SELECT * FROM flights WHERE dep_delay > 10;
~~~

## data studio
- 모두 drag & drop 방식이라 메모할 내용이 없음

## comment
- Data Studio는 네비게이션 메뉴에도 없고 검색으로도 나오지 않는 다는 것은 GCP의 일부가 아닌 것인가?  http://datastudio.google.com/ 로 접근하도록 되어있음.
- 사용하던 GCP 계정으로 연결되어서 로그아웃을 해 준 다음에 진행해야 오류가 없음
- 데이터 내용을 모르니까 시각화도 재미가 없음. scatter chart까지만 그려보고 종료

