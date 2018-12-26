# App Engine Qwik Start
> https://google.qwiklabs.com/catalog_lab/702

## Summary
- App Engine을 통해 빠르게 applicatin을 배포를 테스트합니다.

## Source
~~~bash
git clone -b phase0-helloworld https://github.com/GoogleCloudPlatform/appengine-php-guestbook.git helloworld
~~~

## app.yaml
~~~
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