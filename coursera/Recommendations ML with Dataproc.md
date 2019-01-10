# Recommendations ML with Dataproc
- Levels: introductory 
- Link: https://googlecoursera.qwiklabs.com/focuses/16384

## Summary
- Cloud SQL을 설치 후 dataproc에서 접속하여 사용하는 예제

## Cheat Sheet
~~~bash
gsutil mb gs://<bucket name>
gsutil ls gs://<bucket name>
gsutil cp cloudsql/* gs://<bucket name>
~~~

## Comment
- Cloud Shell 설치할 때 add network에 cloud shell ip를 찾아서 넣음
~~~
wget -qO - http://ipecho.net/plain; echo
~~~