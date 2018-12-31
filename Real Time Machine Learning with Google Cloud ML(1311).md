# Real Time Machine Learning with Google Cloud ML
- Levels: advanced
- Link: https://www.qwiklabs.com/catalog_lab/1311

## Summary
- simulate.py 를 사용해서 pub/sub으로 실시간 데이터 생성을 시뮬레이션
- 다른 SSH를 하나 더 띄워서 확인

## Simulation
~~~
$ git clone  https://github.com/GoogleCloudPlatform/data-science-on-gcp/
$ cd ~/data-science-on-gcp/04_streaming/simulate
$ python ./simulate.py --project $PROJECT_ID --startTime '2015-01-01 06:00:00 UTC' --endTime '2015-01-03 00:00:00 UTC' --speedFactor=60

To generate an access token for other uses, run:
  gcloud auth application-default print-access-token
(env) $ cd ~/data-science-on-gcp/04_streaming/simulate
(env) $ export PROJECT_ID=$(gcloud info --format='value(config.project)')
(env) $ python ./simulate.py --project $PROJECT_ID --startTime '2015-01-01 06:00:00 UTC' --endTime '2015-01-03 00:00:00 UTC' --speedFactor=60
/home/google1978934_student/data-science-on-gcp/04_streaming/simulate/env/local/lib/python2.7/site-packages/google/auth/_default.py:66: UserWarning: Your application has authenticated using end user credentials from Google Cloud SDK. We recommend that most server applications use service accounts instead. If your application continues to use end user credentials from Cloud SDK, you might receive a "quota exceeded" or "API not enabled" error. For more information about service accounts, see https://cloud.google.com/docs/authentication/
  warnings.warn(_CLOUD_SDK_CREDENTIALS_WARNING)
Simulation start time is 2015-01-01 06:00:00+00:00
INFO: Publishing 0 wheelsoff till 2015-01-01T06:05:00-00:00
INFO: Publishing 0 arrived till 2015-01-01T06:05:00-00:00
INFO: Publishing 1 departed till 2015-01-01T06:05:00-00:00
INFO: Sleeping 2.0 seconds
INFO: Publishing 1 wheelsoff till 2015-01-01T06:08:00-00:00
INFO: Publishing 0 arrived till 2015-01-01T06:08:00-00:00
INFO: Publishing 0 departed till 2015-01-01T06:08:00-00:00
INFO: Sleeping 3.0 seconds
INFO: Publishing 1 wheelsoff till 2015-01-01T06:11:00-00:00
INFO: Publishing 0 arrived till 2015-01-01T06:11:00-00:00
INFO: Publishing 1 departed till 2015-01-01T06:11:00-00:00
INFO: Sleeping 3.0 seconds
INFO: Publishing 2 wheelsoff till 2015-01-01T06:14:00-00:00
INFO: Publishing 0 arrived till 2015-01-01T06:14:00-00:00
INFO: Publishing 1 departed till 2015-01-01T06:14:00-00:00
INFO: Sleeping 3.0 seconds
INFO: Publishing 0 wheelsoff till 2015-01-01T06:21:00-00:00
~~~

## PubSub 
- Topic 만들기
~~~
gcloud pubsub topics create dataflow_temp
~~~

## Dataflow
~~~
mvn compile exec:java \
-Dexec.mainClass=com.google.cloud.training.flights.AddRealtimePrediction \
 -Dexec.args="--realtime --speedupFactor=60 --maxNumWorkers=10 --autoscalingAlgorithm=THROUGHPUT_BASED --bucket=$BUCKET --project=$PROJECT_ID"
~~~

## Big Query
~~~
SELECT * from flights.predictions ORDER by notify_time DESC LIMIT 5
~~~

## Comment
- Cloud Shell이 아니라 VM에서 실행해야 함
- Application credentials에 설치할 때 중간에 https://url이 나오는데 이 url을 클릭해야 verification code를 얻을 수 있음
~~~bash
(env) $ gcloud auth application-default login

Go to the following link in your browser:
    https://accounts.google.com/o/oauth2/auth?redirect_uri=urn%3Ai...
    Enter verification code:
~~~