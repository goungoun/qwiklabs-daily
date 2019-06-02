# Running Dedicated Game Servers in Google Kubernetes Engine

## Summary
~~~bash
# VM을 생성하고 SSH 콘솔로 접속하여 kubectl과 docker를 설치
sudo apt-get update
sudo apt-get -y install kubectl
sudo apt-get -y install \
     apt-transport-https \
     ca-certificates \
     curl \
     gnupg2 \
     software-properties-common

# Docker install
curl -fsSL https://download.docker.com/linux/$(. /etc/os-release; echo "$ID")/gpg | sudo apt-key add -
sudo apt-key fingerprint 0EBFCD88
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/$(. /etc/os-release; echo "$ID")    $(lsb_release -cs) stable"
sudo apt-get -y install docker-ce 
sudo usermod -aG docker $USER
docker run hello-world

# Dedicated game server
export GCR_REGION=<REGION> PROJECT_ID=<PROJECT_ID>
printf "$GCR_REGION \n$PROJECT_ID\n"
export GCR_REGION=us PROJECT_ID=qwiklabs-gcp-e5xxxxxxx4c61da8

export GCR_REGION=<REGION> PROJECT_ID=<PROJECT_ID>
printf "$GCR_REGION \n$PROJECT_ID\n"
export GCR_REGION=us PROJECT_ID=qwiklabs-gcp-e5xxxxxxx4c61da8
cd gke-dedicated-game-server/openarena
docker build -t \
${GCR_REGION}.gcr.io/${PROJECT_ID}/openarena:0.8.8 .
gcloud docker -- push \
  ${GCR_REGION}.gcr.io/${PROJECT_ID}/openarena:0.8.8
~~~
생략

## Comment
- 게임 서버를 컨테이너로 개발하는 것이 빠르게 확산되고 있다고 하는데 왜 그럴까? 인프라를 구축할 때 게임이 성공할지 실패할지, 유저가 폭주할지 그렇지 않을지 예측하기가 어렵고 준비 기간이 길기 때문일 것 같다. (오버 프로비저닝, 언더 프로비저닝)

