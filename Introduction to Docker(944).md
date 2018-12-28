# Introduction to Docker
- Levels: introductory
- Link: https://www.qwiklabs.com/catalog_lab/944

# Summary
- Node.js로 hello world를 출력하는 서버를 만들고 (v.1.0)
- hello world를 hello cloud로 바꿔서 2.0버전으로 업그레이드 하는 예제
- 도커 사용 관련 필수 명령어
~~~bash
docker run hello-world
docker run --name [container-name] hello-world.
docker ps
docker ps -a
docker build -t node-app:0.1 . #  현재의 디렉토리에 Dockerfile이 있어야 함
docker run -p 4000:80 --name my-app node-app:0.1
docker logs [container_id]
docker logs -f [container_id]
docker images
docker tag node-app:0.2 gcr.io/[project-id]/node-app:0.2
gcloud docker -- push gcr.io/[project-id]/node-app:0.2
~~~
> https://docs.docker.com/engine/reference/commandline/docker/
- keyword
`docker hub` `Dockerfile` `gcr.io`

## Hello World
- Docker Hub public registry로부터 hello-world 컨테이너 이미지를 가져와서 실행
- hello-world 컨테이너는 금방 종료되기 때문에 -a 옵션을 사용해서 Exited 된 컨테이너도 살펴본다.
- hello-world 컨테이너의 NAME이 랜덤으로 아무거나 표시가 되는데 (여기서는 awesome_wing) 이것을 지정해주려면 run 할 때 `--name` 옵션을 사용
~~~bash
$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                     PORTS               NAMES
0c89b2f61aca        hello-world         "/hello"            6 minutes ago       Exited (0) 6 minutes ago                       awesome_wing
~~~

## Build
- Dockerfile
~~~bash
FROM node:6
WORKDIR /app
ADD . /app
EXPOSE 80
CMD ["node", "app.js"]
~~~
> docker run 할 떄 `$ node app.js`가 실행되면서 서버 동작 

## Run
- Dockerfile에서 80번으로 오픈한 것을 실행중인 서버의 4000포트로 연결
~~~
$ docker run -p 4000:80 --name my-app -d node-app:0.1
5feb79bb6adb3200f0ff2ac2f0ebb4bcf27a8b4f10d77809ccba48be2d4318cb
$ docker logs -f 5feb79bb6adb 
Server running at http://0.0.0.0:80/
~~~
> 4000포트로 열어서 봐야 하지만 로그에는 컨테이너 내부 포트인 80으로 찍힘
- 80포트가 4000포트로 연결된 사실은 docker ps 에서 PORTS를 확인
~~~
$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                  NAMES
5feb79bb6adb        node-app:0.1        "node app.js"       8 minutes ago       Up 8 minutes        0.0.0.0:4000->80/tcp   my-app
~~~
- curl
~~~
$ curl http://0.0.0.0:4000
Hello World
$ curl http://0.0.0.0:80
0.1/
computeMetadata/
~~~
> 80포트는 cloud shell에서 이미 사용

## Debug
- pseudo-tty `-it`
~~~
$ docker exec -it [container_id] bash
$ exit
~~~
> 빌드한 도커 이미지에 무슨 파일이 들어가있는지 보고 싶을 때 위 명령어를 실행시키면 WORKDIR에서 지정한 /app 디렉토리로 들어감

## Publish
- gcr.io
~~~
$ docker tag node-app:0.2 gcr.io/[project-id]/node-app:0.2
$ gcloud docker -- push gcr.io/[project-id]/node-app:0.2
docker images
~~~

## Commment
- 쿠버네티스 해커톤하고 나면 익숙해짐
