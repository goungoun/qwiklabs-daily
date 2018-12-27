## Building an IoT Analytics Pipeline 
- Levels: advanced
- Link: https://www.qwiklabs.com/catalog_lab/694

## Cloud Shell
- Persistent 5 GB home directory
- gcloud command
- 출력 형식만 지정하려면 --format 플래그를 사용하여 표 형태나 일반 형태의 출력(대화식 표시) 또는 머신이 읽을 수 있는 형태(json, csv, yaml, value)로 표시
> https://cloud.google.com/sdk/gcloud/
~~~
gcloud auth list
gcloud config list project
~~~

## API & Services
- Google Cloud IoT API
- Google Cloud Pub/Sub API
- Dataflow API

## Pub/Sub
- 비동기 global message service
- Decoupling senders and receivers 
- Highly available communication between independently written applications
- low-latency, durable messaging

## Cloud IoT (Internet of Thinkngs)Core
- Fully managed service: 연결, 관리, 데이터 입수
- 표준 MQTT (Message queue Telemetry Transport)와 HTTP를 지원함
- Device manager: 장치 등록
- Protocol bridge: 장치가 Google Cloud Platform을 사용할 수 있도록 해줌
- Device를 Cloud IoT Core resource의 형태로 registry에 등록한다.

## MQTT-based devices using Cloud IoT core
- How to simulate?

## Dataflow
- source: Pub/Sub topic 
- target: Big Query table
- Options: maxNumWorkers(default=3)
- Job save
> gs://dataflow-templates-libraries/2018-11-26-00_RC00/pipeline-dAXW53a5l4o2O7vZ_PfhUQ.pb

## Simulator
- 이 부분을 자세하게 봐야함
- install: python-pip openssl git
- pip install: pyjwt paho-mqtt cryptography
~~~bash
sudo apt-get install python-pip openssl git -y
sudo pip install pyjwt paho-mqtt cryptography
~~~
- git clone
~~~bash
git clone http://github.com/GoogleCloudPlatform/training-data-analyst
~~~
- RSA key gen
~~~bash
cd $HOME/training-data-analyst/quests/iotlab/
# This openssl command creates an RSA cryptographic keypair and writes it to a file called rsa_private.pem
openssl req -x509 -newkey rsa:2048 -keyout rsa_private.pem \
    -nodes -out rsa_cert.pem -subj "/CN=unused"
~~~
- 장치를 IoT Core에 연동하기 위해서 registry에 등록
- 장치의 위치: 부에노스 아이레스와 이스탄불 두 군데에 장치가 있음
- registry는 iotlab-registry
~~~bash
gcloud beta iot registries create iotlab-registry \
   --project=$PROJECT_ID \
   --region=$MY_REGION \
   --event-notification-config=topic=projects/$PROJECT_ID/topics/iotlab

gcloud beta iot devices create temp-sensor-buenos-aires \
  --project=$PROJECT_ID \
  --region=$MY_REGION \
  --registry=iotlab-registry \
  --public-key path=rsa_cert.pem,type=rs256
~~~

~~~bash
gcloud beta iot devices create temp-sensor-istanbul \
  --project=$PROJECT_ID \
  --region=$MY_REGION \
  --registry=iotlab-registry \
  --public-key path=rsa_cert.pem,type=rs256
~~~

## Big Query
~~~sql
#standardsql
SELECT timestamp, device, temperature 
  FROM iotlab.sensordata
 ORDER BY timestamp DESC
LIMIT 100
~~~

## Trouble Shooting
- 기 사용 gmail 계정을 로그인 후 secret mode에서 실습하는 것으로 오류를 방지
- 권한 오류로 QwikLab에 문의를 많이 올렸으나 이슈가 재현되지 않음

## Comment
- 프로젝트명으로 버켓을 만드는 이유는 Dataflow를 생성할 때 임시데이터를 저장하는 설정으로 사용
- 그러면 DataFlow 사용 시에는 항상 버켓을 만들어야 하는 것인가?