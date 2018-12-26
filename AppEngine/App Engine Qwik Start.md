# App Engine Qwik Start
> PHP https://google.qwiklabs.com/catalog_lab/702

## Summary
- App Engine을 통해 빠르게 applicatin을 배포합니다.

## Source
~~~bash
git clone -b phase0-helloworld https://github.com/GoogleCloudPlatform/appengine-php-guestbook.git helloworld
~~~

## app.yaml
~~~yaml
runtime: php55
api_version: 1
threadsafe: true

handlers:
- url: /.*
  script: helloworld.php
~~~

## Test
- 구글 클라우드 개발 서버는 `dev_appserver.py`에 구현되어 있으며 여기에는 기 설치된 App Engine SDK가 포함되어있습니다.
~~~bash
dev_appserver.py app.yaml --php_executable_path /usr/bin/php-cgi
~~~

## Deploy
- `app.yaml`이 존재하는 디렉토리에서 `gcloud app deploy`명령어를 사용하여 배포합니다.
~~~
gcloud app deploy
gcloud app logs tail -s default
gcloud app browse
~~~

## Comment
- cloud shell 커맨드처럼 보이지도 않고 php도 아닌 파이썬 파일 `dev_appserver.py`로 테스트하는 부분이 자연스러워 보이지 않았습니다.
- `dev_appserver.py`는 예제 소스에 존재하는 파일이 아니었습니다.
- 위 예제는 PHP 예제인데 Java의 경우는 https://google.qwiklabs.com/catalog_lab/917 를 참고하면 되고 maven `mvn appengine:devserver`를 사용하여 테스트합니다.
- App Engine에서 Java7, Java8, Python 2.7, Go and PHP를 지원한다고 하는데 버전들이 많이 OLD한듯 합니다. Python 3.7은 2018년 12월 현재 beta 입니다.
https://cloud.google.com/appengine/docs/standard/#index_of_features