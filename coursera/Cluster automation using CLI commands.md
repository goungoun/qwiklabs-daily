# Cluster automation using CLI commands v1.3
- Levels: fundamental
- Link: https://googlecoursera.qwiklabs.com/catalog_lab/1366

## Summary
- 커맨드라인으로 클러스터를 셋업하는 법
- 별도로 모든 노드에 설치가 필요한 부분이 있으면 스크립트를 작성해서 `--initialization-actions` 에 입력

## init-script.sh 예시
- 모든 노드에 파이썬을 설치해주고 master node에만 소스코드를 git clone 해준다.
> 특히 cluster에서 jupyter notebook을 접근할 수 있도록 해 주려면 gs://dataproc-initialization-actions/datalab/datalab.sh이 추가되어야 함
~~~bash
#!/bin/bash

# install Google Python client on all nodes
apt-get update
apt-get install -y python-pip
pip install --upgrade google-api-python-client

ROLE=$(/usr/share/google/get_metadata_value attributes/dataproc-role)
if [[ "${ROLE}" == 'Master' ]]; then
   git clone https://github.com/GoogleCloudPlatform/training-data-analyst
fi
~~~

- 설치후 bucket으로 업로드 <br>
`gsutil cp ./init-script.sh gs://$BUCKET`

## Create Cluster
- dataproc tags: 방화벽을 적용하기 위한 네트워크 태그로 방화벽 생성할 때 --target-tags옵션에 적용하여 줌
~~~bash
gcloud dataproc clusters create cluster-custom \
--bucket $BUCKET \
--subnet default \
--zone $MYZONE \
--region $MYREGION \
--master-machine-type n1-standard-2 \
--master-boot-disk-size 100 \
--num-workers 2 \
--worker-machine-type n1-standard-1 \
--worker-boot-disk-size 50 \
--num-preemptible-workers 2 \
--image-version 1.2 \
--scopes 'https://www.googleapis.com/auth/cloud-platform' \
--tags customaccess \
--project $PROJECT_ID \
--initialization-actions 'gs://'$BUCKET'/init-script.sh','gs://dataproc-initialization-actions/datalab/datalab.sh'
~~~

## Firewall
~~~bash
$ gcloud compute \
 --project=$PROJECT_ID \
 firewall-rules create allow-custom \
 --direction=INGRESS \
 --priority=1000 \
 --network=default \
 --action=ALLOW \
 --rules=tcp:9870,tcp:8088,tcp:8080 \
 --source-ranges=$BROWSER_IP/32 \
 --target-tags=customaccess
~~~
- 결과
~~~
NAME          NETWORK  DIRECTION  PRIORITY  ALLOW                       DENY  DISABLED
allow-custom  default  INGRESS    1000      tcp:9870,tcp:8088,tcp:8080        False
~~~ 

## Comment
- cloud bucket은 gsutil 명령어로 사용 (gcloud 명령어가 아님) 
- DataLab 설치 방법과 스크립트 위치 중요
> 스크립트의 내용이 궁금하면 gsutil cat gs://dataproc-initialization-actions/datalab/datalab.sh
- 만든다음에 확인해야할 URL : Yarn application monitoring `<master_ip>:8088`, DataLab `<master_ip>:8080`, Hadoop admin `<master_ip>:8080`