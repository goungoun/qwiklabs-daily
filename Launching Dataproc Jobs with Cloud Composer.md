# Launching Dataproc Jobs with Cloud Composer
- Levels: advanced
- Link: https://www.qwiklabs.com/catalog_lab/1285

## Summary
- On-prem 데이터를 GCS로 업로드해서 DataProc의 Spark JOB을 돌린 후 BigQuery로 로드하는 아키텍처
- ETL Architecture
![Architecture](https://gcpstaging-qwiklab-website-prod.s3.amazonaws.com/bundles/assets/56058a36ddf8a382b0d436b7e980e1788b51546b5091bbbc88dc5f92ef6698b2.png)
> Spark에서 읽어갈 수 있도록 google cloud storage쪽으로 데이터를 올린 후에 (on-prem 영역) 데이터를 처리한 후 Big Query에 입력. 데이터가 입력되면 GCS와 DataProc cluster 둘다 깨끗이 지워준다. (x 표시된 부분)

## CheetSheet
~~~bash
bq mk, bq query, bq rm -f, bq extract
~~~

## Prep
- 실제 on-prem 데이터로 실습하면 좋겠지만 이 Lab에서는 Public dataset중 뉴욕 택시 데이터를 1일치를 받아서 이 데이터가 On-prem 데이터인셈 치고 GCS로 올려준다. 아래 예제는 ComposeDemo 데이터셋을 만들어서 테이블을 만든 뒤 `bq extract` 커맨드를 사용해서 json으로 만들어 GCS로 올려주는 예제
~~~bash
bq mk ComposerDemo
$ bq query --destination_table=ComposerDemo.nyc_tlc_yellow_nyd_2015 --use_legacy_sql=false --allow_large_results "SELECT * FROM \`nyc-tlc.yellow.trips\` WHERE EXTRACT(DATE FROM dropoff_datetime) = '2015-01-01'"
$ export EXPORT_TS=`date "+%Y-%m-%dT%H%M%S"` && bq extract --destination_format=NEWLINE_DELIMITED_JSON \
ComposerDemo.nyc_tlc_yellow_nyd_2015 \
gs://$PROJECT/cloud-composer-lab/raw-$EXPORT_TS/nyc-tlc-yellow-*.json # 업로드
$ bq rm -f -t ComposerDemo.nyc_tlc_yellow_nyd_2015 # 삭제
~~~

## Cloud Composer 만들기 (=Apache airflow 셋업)
~~~bash
gcloud composer environments create demo-ephemeral-dataproc \
--location us-central1 \
--zone us-central1-b \
--machine-type n1-standard-2 \
--disk-size 20
~~~
> 15분 소요 (11:29~) 
> Cloud composer는 web UI를 public IP로 오픈하기 때문에 보안이 걱정되는 경우에는 Identity Aware Proxy를 참고

## Comment
- DataProc cluster만 만들면 1분 30초 걸리는데 Cloud Composer는 15분이나 걸림
- 쓰고 지울 용도인데 Airflow까지 동원하는 사용하는 이유는? JOB의 선후행 관계가 복잡한 DW의 경우 이렇게 셋업해서 쓰는 것인가? 
