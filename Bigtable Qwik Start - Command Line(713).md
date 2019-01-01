# Bigtable: Qwik Start - Command Line
- Levels: introductory
- Link: https://www.qwiklabs.com/catalog_lab/713

## Summary
- cbt 커맨드를 활성화하여 cloud shell에서 Bigtable을 만들고 column family를 추가해보는 예제
~~~
cbt createtable my-table
cbt createfamily my-table cf1
cbt ls
cbt ls my-table
cbt set my-table r1 cf1:c1=test-value
cbt read my-table
cbt deletetable my-table
~~~
- Keywords: `cbt` `~/.cbtrc` `column family` `column qualifier`

## CBT 커맨드 활성화
- cloud shell의 ~/.cbtrc 파일에 프로젝트명과 BigTable 생성 정보를 기록
~~~bash
project = [project_id]
instance = quickstart-instance
~~~

## CBT
- 테이블 생성, 컬럼 패밀리 cf1 추가
~~~bash
$ cbt createtable my-table
$ cbt ls
$ cbt createfamily my-table cf1
~~~
~~~
$ cbt ls my-table
2019/01/02 02:00:49 -creds flag unset, will use gcloud credential
Family Name     GC Policy
-----------     ---------
cf1             <never>
~~~
- 데이터 입력: row `r1`을 column family cf1의 column qualifier `c1`에 추가
~~~bash
$ cbt set my-table r1 cf1:c1=test-value
$ cbt read my-table
2019/01/02 02:06:11 -creds flag unset, will use gcloud credential
----------------------------------------
r1
  cf1:c1                                   @ 2019/01/02-02:04:47.546000
    "test-value"
$
~~~
- 삭제
~~~bash
$ cbt deletetable my-table
~~~

## Comment
- 클러스터 생성할 때의 가격정보
~~~
1,593.50 per month (1,000 GB data, 3 nodes) 
~~~
- cbt set 커맨드로 1row를 입력할 때 RDB처럼 빠르지는 않음. 5초 정도 소요
- 모니터링 UI에 초당 읽기/쓰기, CPU 사용률 등을 확인할 수 있는데 기본 커맨드로는 살펴볼 수 있는 내용이 없고 workload를 생성하는 벤치마킹 도구가 필요