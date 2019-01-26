# Streaming IoT Data to Google Cloud Storage
- Levels: fundamental
- Permalink: https://www.qwiklabs.com/catalog_lab/1272

## Cryptography Keypair 
- rsa_private.pem 파일 생성
~~~bash
cd $HOME/training-data-analyst/quests/iotlab/
openssl req -x509 -newkey rsa:2048 -keyout rsa_private.pem \
    -nodes -out rsa_cert.pem -subj "/CN=unused"
~~~
- iot core > add device에 using rsa_cert.pem를 등록하고 Public key format은 RS256-X509로 선택
- device 등록 할 때 buenos-aires와 istanbul 2군데를 등록

## Simulator
- Json을 만들어내는 fake client `cloudiot_mqtt_example_json.py`
https://github.com/GoogleCloudPlatform/training-data-analyst/blob/master/quests/iotlab/cloudiot_mqtt_example_json.py
- 2군데의 device를 시뮬레이션 하기 위해 파라미터를 넘긴다. 동일한 설정 device_id만 다름
~~~bash
python cloudiot_mqtt_example_json.py \
   --project_id=$PROJECT_ID \
   --cloud_region=$MY_REGION \
   --registry_id=iotlab-registry \
   --device_id=temp-sensor-buenos-aires \
   --private_key_file=rsa_private.pem \
   --message_type=event \
   --algorithm=RS256 --num_messages=200 > buenos-aires-log.txt 2>&1 &
~~~
~~~bash
python cloudiot_mqtt_example_json.py \
   --project_id=$PROJECT_ID \
   --cloud_region=$MY_REGION \
   --registry_id=iotlab-registry \
   --device_id=temp-sensor-istanbul \
   --private_key_file=rsa_private.pem \
   --message_type=event \
   --algorithm=RS256 \
   --num_messages=200
~~~
- json 예제
~~~bash
{ "device": "temp-sensor-buenos-aires", "timestamp": 1533829204, "temperature": 15.80888889949552 } { "device": "temp-sensor-buenos-aires", "timestamp": 1533829205, "temperature": 15.799777248019161 }
~~~

## Comment
- 버켓에 파일이 5분뒤에 생기는 이유?