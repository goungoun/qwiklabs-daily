# Interact with Google Cloud Storage

## Cheat Sheet
~~~bash
gsutil acl ch -u AllUsers:R gs://<YOUR-BUCKET>/earthquakes/*
less ingest.sh
sudo apt-get update
sudo apt-get -y -qq --fix-missing install python3-mpltoolkits.basemap python3-numpy python3-matplotlib python3-requests
gsutil cp earthquakes.* gs://<YOUR-BUCKET>/earthquakes/
gsutil ls gs://<YOUR-BUCKET>earthquakes/
gsutil acl ch -u AllUsers:R gs://<YOUR-BUCKET>/earthquakes/*
~~~
> apt-get --fix-missing은 update한 후에 어떤 문제가 있어서 동작하지 않을 수 있는 부분들을 스스로 install하라는 옵션이라고 함 (출처: 스택오퍼블로우)
> https://askubuntu.com/questions/462690/what-does-apt-get-fix-missing-do-and-when-is-it-useful

## comment
- compute engine을 cloud shell에서 명령어로 만들었더니 이런 WARNING이 뜸. 새로 만들어진 프로젝트에 SSH keys가 없어서 
~~~bash
WARNING: The public SSH key file for gcloud does not exist.
WARNING: The private SSH key file for gcloud does not exist.
WARNING: You do not have an SSH key for gcloud.
WARNING: SSH keygen will be executed to generate a key.
This tool needs to create the directory
[/home/google2135182_student/.ssh] before being able to generate SSH
keys.
~~~
- cat이나 vi에 비해 개인적인 취향으로 less는 불편함
