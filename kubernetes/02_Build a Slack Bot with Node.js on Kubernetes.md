# Build a Slack Bot with Node.js on Kubernetes

## Keyword
`secret`

## Summary
- docker
~~~bash
# 로컬에서 도커 실행하고 registry에 올리기
$ docker run -d \
    -v $(pwd)/:/config \
    -e slack_token_path=/config/slack-token \
    gcr.io/${PROJECT_ID}/slack-codelab:v1
$ docker ps
$ docker stop [your-docker-id]

# Container Registry > Images
$ gcloud docker -- push gcr.io/${PROJECT_ID}/slack-codelab:v1
~~~
- kubernetes
~~~bash

# 쿠버네티스 클러스터 만들기
$ gcloud container clusters create my-cluster \
      --num-nodes=2 \
      --zone=us-central1-f \
      --machine-type n1-standard-1
$ gcloud compute instances list

# 슬랙 토큰 저장할 쿠버네티스 시크릿
$ kubectl create secret generic slack-token --from-file=./slack-token
$ kubectl create -f slack-codelab-deployment.yaml --record
$ kubectl rollout history deployment/slack-codelab
$ kubectl get pods

# slack-codelab-deployment.yaml 만들고 배포
$ kubectl create -f slack-codelab-deployment.yaml --record
$ kubectl rollout history deployment/slack-codelab

# v2로 업그레이드: 캐싱 되어있어서 도커 빌드는 좀 빠르게 된다
$ kubectl apply -f slack-codelab-deployment.yaml
$ docker build -t gcr.io/${PROJECT_ID}/slack-codelab:v2 .
$ gcloud docker -- push gcr.io/${PROJECT_ID}/slack-codelab:v2

# 배포 이력 확인
$ kubectl rollout history deployment/slack-codelab

# 계속 커맨드를 날려봐서 상태가 running이 되면 slack이 다시 online이 됨 (몇 분 정도 소요..)
$ kubectl get pods
~~~

- slack-codelab-deployment.yaml
~~~yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: slack-codelab
spec:
  replicas: 1
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        app: slack-codelab
    spec:
      containers:
      - name: master
        image: gcr.io/PROJECT_ID/slack-codelab:v1  # Replace PROJECT_ID
                                                   # with your project ID.
        volumeMounts:
        - name: slack-token
          mountPath: /etc/slack-token
        env:
        - name: slack_token_path
          value: /etc/slack-token/slack-token
      volumes:
      - name: slack-token
        secret:
          secretName: slack-token
~~~

## Troubld Shooting
~~~bash
$ kubectl logs slack-codelab-8db78dc45-9r99f
Error from server (BadRequest): container "master" in pod "slack-codelab-8db78dc45-9r99f" is waiting to start: ContainerCreating
~~~

## Comment
- 슬랙의 기능 중에 Incomming webhook은 시스템 운영과 관련한 알람을 받는 용도로 많이 쓰이는 기능임
> Slack apps management page > Features > Incoming webhook

토큰
xoxb-652492574416-654666459366-uzfoHbnbMWuUdETYTlnx7sPC

https://hooks.slack.com/services/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

## Discussion
- 커맨드를 학습하기 좋은 랩, 하지만 쿠버네티스가 가지는 분산 처리의 기능은 활용하지 않음
- 도커를 모르는 사람도 랩을 실습할 수 있게 만들어져 있음. docker를 안 쓴 어플리케이션 부터 시작해서 Dockerfile을 만들어서 도커 이미지를 빌드하고 registry로 올리며 deployment를 만들어서 kubernetes 클러스터로 적용하는 부분까지 순서대로 잘 설명 
- kubernetes secret에 slack token을 저장하는 부분을 커버하고 있는 부분도 좋음
- 뭔가 잘못했을 때에 대처할 수 있는 시나리오가 부족. 예를 들어 Pod의 상태가 `Running`으로 안 바뀌고 `ContainerCreating` 상태로 머무르는 경우에 log를 확인하는 부분이 없고 도커 이미지를 잘못 만들었을 때 삭제하는 방법에 대한 가이드가 없음
