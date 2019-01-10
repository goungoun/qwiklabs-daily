# Create a Compute Engine Instance

## Cheat Sheet
~~~bash
gcloud compute --project "qwiklabs-gcp-13c32fc296aa5ffd" ssh --zone "us-central1-a" "instance-1"

cat /proc/cpuinfo

Intel(R) Xeon(R) CPU @ 2.60GHz
cpu MHz         : 2600.000
cpu cores       : 1

sudo apt-get update
sudo apt-get -y -qq install git
~~~
> -q : 로그 메시지를 출력한다(진행 상태는 표시되지 않는다).
> -qq : 메시지를 출력하지 않는다(단, 오류는 출력한다).
> -y : 모든 질문에 대해 예(yes)로 대답하고, 물어보지 않는다.

## comment
- 묻지말고 설치하는 (단, 오류만 출력하는) 방법으로 git을 설치해보았다.
- 데비안 계열의 패키징 관리의 우월함(?)