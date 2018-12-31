# Hello Node Kubernetes
- Levels: advanced
- Link: https://www.qwiklabs.com/catalog_lab/468

## Summary
- Node.js를 사용하여 간단한 웹서버를 만들고 dokerizing하여 kubernetes에 배포해본다
- 배포 후에 버전을 올려서 Roll out 방식 (기본)으로 배포해보고 replica 수도 줄이거나 늘려본다
- Keyword: `Dockerfile` `gcr.io` `container registry` `kubernetes` `pod`

## Source
- server.js
~~~js
var http = require('http');
var handleRequest = function(request, response) {
  response.writeHead(200);
  response.end("Hello World!");
}
var www = http.createServer(handleRequest);
www.listen(8080);
~~~

## Docker
- Dockerfile
~~~
FROM node:6.9.2
EXPOSE 8080
COPY server.js .
CMD node server.js
~~~
- Docker build & run
~~~bash
export PROJECT_ID=$(gcloud info --format='value(config.project)')
docker build -t gcr.io/${PROJECT_ID}/hello-node:v1 .
docker run -d -p 8080:8080 gcr.io/${PROJECT_ID}/hello-node:v1
~~~
> docker push 하지 않으면 container registry로 안 올라감에 주의
- 다른 cloud shell에서 확인
~~~
curl http://localhost:8080
docker ps
~~~

## Container Registry
- Docker push
~~~
export PROJECT_ID=$(gcloud info --format='value(config.project)')
gcloud docker -- push gcr.io/${PROJECT_ID}/hello-node:v1
~~~
> gcr.io에 올라간 Container image를 확인하려면 Tools 메뉴 아래 있다.
> gcloud docker push에서는 PROJECT_ID를 파라미터로 넘길 수 없음.. 이 아니라 다른 shell에서 띄웠으면 PROJECT_ID를 다시 세팅해줘야함

## Kubernetes
- 노드 2개짜리 클러스터를 생성
~~~bash
gcloud container clusters create hello-world \
                --num-nodes 2 \
                --machine-type n1-standard-1 \
                --zone us-central1-a
~~~
> 만드는데 걸리는 시간은 약 1분 30초
- POD란? 관리나 네트워킹의 목적으로 컨테이터 1개 이상을 묶어놓은 것
- gcr.io에서 꺼내다가 쿠버네티스를 실행
~~~bash
kubectl run hello-node \
    --image=gcr.io/${PROJECT_ID}/hello-node:v1 \
    --port=8080
~~~
> deployment "hello-node" created라고 나오는데 deployment는 pod를 생성하거나 scale out 할 때 쓰임
~~~bash
kubectl get deployments
kubectl get pods
kubectl logs <pod-name>
~~~
> 이 외에도 kubectl cluster-info, kubectl config view, kubectl get events도 있음 

## Loadbalencer
- expose 
~~~
kubectl expose deployment hello-node --type="LoadBalancer"
~~~
> 기본적으로 POD는 클러스터 내 internal IP로만 접근 가능하기 때문에 가상 네트워크 외부에서 접근이 안됨
- scale
~~~
kubectl scale deployment hello-node --replicas=4
$ kubectl get pods
NAME                          READY     STATUS              RESTARTS   AGE
hello-node-69d46bc79d-45x6d   1/1       Running             0          8m
hello-node-69d46bc79d-828k2   1/1       Running             0          10s
hello-node-69d46bc79d-b6qmc   1/1       Running             0          10s
hello-node-69d46bc79d-w5xmt   0/1       ContainerCreating   0          10s
~~~
> 4개로 늘어남

## Upgrade & Roll out
- node.js를 수정 후 v2로 올림
~~~bash
docker build -t gcr.io/${PROJECT_ID}/hello-node:v2 .
gcloud docker -- push gcr.io/${PROJECT_ID}/hello-node:v2
kubectl edit deployment hello-node # 에디트 창이 열리면 spec.containers.image를 v2로 변경
~~~
> Kubernetes will smoothly update your replication controller to the new version of the application.
~~~bash
$ kubectl get deployments
NAME         DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
hello-node   4         5         4            3           16m
$ kubectl get pods
NAME                          READY     STATUS        RESTARTS   AGE
hello-node-69d46bc79d-45x6d   1/1       Terminating   0          17m
hello-node-84d67dc87f-4pb44   1/1       Running       0          1m
hello-node-84d67dc87f-jcxwh   1/1       Running       0          1m
hello-node-84d67dc87f-jz2bf   1/1       Running       0          1m
hello-node-84d67dc87f-ndnp9   1/1       Running       0          1m
~~~
> 이미지가 변경되니까 pod수가 순간적으로 5개가 되고 1개는 종료되는 중임. 정상적으로 완료되면 DESIRED, CURRENT, UP-TO-DATE, AVAILABLE 모두 4개가 됨
> 잘 실행되는지 확인하려면 `kubectl get services`로 hello-node(LoadBalancer) external IP를 확인


## Trouble Shooting
- SyntaxError: missing ) after argument list 
~~~
/home/google1983171_student/server2.js:4
  response.end(Hello World!);
               ^^^^^
~~~
> echo "소스코드" > server.js로 하면 따옴표가 먹어서 오류가 남

## Comment
- 쿠버네티스 클러스터가 금방 만들어지기 때문에 동그라미가 뱅글뱅글 돌아가는 것을 쳐다보고 있어도 그다지 상관없음
- POD에 대한 개념은 잘 알아둘 필요