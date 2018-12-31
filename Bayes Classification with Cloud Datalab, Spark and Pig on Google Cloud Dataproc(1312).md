# Bayes Classification with Cloud Datalab, Spark and Pig on Google Cloud Dataproc
Levels: advanced
Link: https://www.qwiklabs.com/catalog_lab/1312

## Summary
- BigQuery에서 데이터를 가져다가 Spark으로 분석하는 예제
- Worker 노드 2개짜리 Dataproc Cluster를 생성
- Master 노드에 설치된 Datalab을 사용하기 위해 방화벽 오픈
- Key words: `Cloud Dataproc` `Cloud Datalab` `Google BigQuery`

## Dataproc cluster
- Deploy the cluster
~~~bash
export DL_INSTALL=gs://$BUCKET/flights/dataproc/datalab.sh
export DS_ON_GCP=gs://$BUCKET/flights/dataproc/install_on_cluster.sh

gcloud dataproc clusters create \
   --num-workers=2 \
   --scopes=cloud-platform \
   --worker-machine-type=n1-standard-2 \
   --master-machine-type=n1-standard-4 \
   --master-boot-disk-type=pd-ssd\
   --master-boot-disk-size=100GB\
   --worker-boot-disk-size=100GB\
   --worker-boot-disk-type=pd-ssd\
   --zone=$ZONE \
   --initialization-actions=$DL_INSTALL,$DS_ON_GCP \
   ch6cluster
~~~
> 옵션 중에서 boot-disk에 ssd를 사용하고 디스크 사이즈를 100G로 설정한 부분에 유의
- master 노드에 tag 붙이기
~~~bash
gcloud compute instances add-tags $MASTERNODE --tags master --zone $ZONE     
~~~
> 태그는 적절하게 VM을 구분하는 용도로 사용하는데 여기에서는 datalab 방화벽 열 때 `--target-tags` 옵션에서 사용

## Datalab
- DataLab access를 위해서 master의 8080 tcp 포트를 오픈
~~~
export MYRANGE=0.0.0.0/0
gcloud compute firewall-rules create datalab-access \
  --allow tcp:8080 \
  --source-ranges $MYRANGE \
  --target-tags master
~~~
> source-range를 0.0.0.0/0으로 주면 모든 IP로부터 master 노드로 들어가는 tcp 요청이 허가 되는데 상용 환경에서는 제한적으로 설정해야 할 것 같음
- 시작
~~~
export MASTERIP=$(gcloud compute instances describe $MASTERNODE --zone $ZONE --format='value(networkInterfaces[0].accessConfigs[0].natIP)')
echo $MASTERIP
http://$MASTERIP:8080
~~~

## Comment
- BigQuery의 정의에 굳이 RESTful webservice를 포함하는 이유가 뭘까?
> Google BigQuery is a RESTful web service that enables interactive analysis of massively large datasets working in conjunction with Google Storage.
- Spark으로 처리할 수 있는 부분에 pig를 꼭 사용해야하는지 모르겠어서 pig는 skip
- 실습 점수처리하는 부분 중에 노트북을 다른 이름으로 저장하도록 되어있는데 점수처리 할 만큼의 중요성이나 의도가 잘 파악이 안됨