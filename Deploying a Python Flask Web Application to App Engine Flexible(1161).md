# Deploying a Python Flask Web Application to App Engine Flexible
- Levels: fundamental
- Link: https://www.qwiklabs.com/catalog_lab/1161
- Source: https://github.com/GoogleCloudPlatform/python-docs-samples/tree/master/codelabs/flex_and_vision

## Summary
- Python Flask를 사용한 얼굴 인식 프로젝트를 앱엔진에 배포해본다.

## About App Engine
- server를 유지보수가 필요 없음
- app을 업로드하면 traffic에 따라 easy to scale
> Load balancing, microservices, authorization, SQL and NoSQL databases, traffic splitting, logging, search, versioning, roll out and roll backs, and security scanning are all supported natively and are highly customizable

## IAM Service account
- app에서 사용할 service account를 받고 key를 home directory에 받고 환경변수에 연결
~~~bash
gcloud iam service-accounts keys create ~/key.json \
--iam-account qwiklab@${PROJECT_ID}.iam.gserviceaccount.com
export GOOGLE_APPLICATION_CREDENTIALS="/home/${USER}/key.json"
~~~

## Setup
- virtualenv 만들기
~~~bash
virtualenv -p python3 env
source env/bin/activate
~~~
https://virtualenv.pypa.io/en/stable/

- 설치에 필요한 것들은 requirements.txt에 기록하여 pip install -r 옵션으로 설치
~~~bash
(env) $ cat requirements.txt
Flask==1.0.2
gunicorn==19.9.0
google-cloud-vision==0.35.0
google-cloud-storage==1.13.0
google-cloud-datastore==1.7.1
(env) $ pip install -r requirements.txt
~~~
- app engine
~~~bash
gcloud app create
~~~

## App engine
-- app.yaml을 수정
> 약간 Dockerfile과 비슷한 느낌?
~~~
gcloud app create
gcloud app deploy
~~~

## Comment
- Scale하는 부분이 완전 자동일까? 수동이고 쉽게 커스터마이징 할 수 있게 되어있는 것일까? 
- 최근 Flask로 빠르게 대충 프로토타이핑 해 놓은 것이 있는데 test case를 어떻게 짜면 좋을까를 고민하고 있던 차에 main_test.py의 예제를 발견하게 되어서 득템한 느낌이었다.
- 배포할 때의 로그를 보면 app engine flexible은 내부적으로 docker를 사용한듯 하다.
~~~
BUILD
Starting Step #0
Step #0: Pulling image: gcr.io/gcp-runtimes/python/gen-dockerfile@sha256:a35ed2864a122cf74594afdf4368d68a402edae91f135c2f309773c1dc407cf9
~~~
- 배포가 너무 천천히 되어서 boost mode 사용하지 않은 것을 후회