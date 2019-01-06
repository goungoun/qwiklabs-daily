# Create a Dataproc Cluster
- Coursera: Data Engineering on Google Cloud Platform
- Levels: fundamental
- Link: https://googlecoursera.qwiklabs.com/catalog_lab/1362

## Summary
- VM과 Dataproc cluster SSH로 연결되게 설정해본다.
- 내 브라우저에서 Spark Cluster 모니터링을 할 수 있도록 방화벽을 해제한다 (http://ip4.me/ 사용)
- Dataproc cluster에서 Google Cloud API를 호출할 수 있도록 설정한다.
- Keywords: `Specified target tag` `Target tags` `ip4.me`

## Prepare Environment Variale
- VM을 사용하는 이유? Cloud Shell은 SLA가 보장되지 않기 때문에 실습 중 꺼질 수 있음
> compute engine의 SLA : https://cloud.google.com/compute/sla

## Create Dataproc Cluster
> network tags : hadoopaccess 로 태그 입력. 방화벽 설정을 위해서 사용

## VPC network 설정
~~~bash
- Name: allow-hadoop
- Type: Ingress
- Targets: hadoopaccess
- Filters: [http://ip4.me/로 얻은 내 노트북 IP]/32
- tcp:9870,8088
- Action: Allow
~~~

## Hadoop Cluster 확인
- http://[master 의 external ip]:8080 에서 Yarn monitoring 확인. JOB 모니터링을 여기서 함
- http://[master 의 external ip]:9870 에서 Hadoop cluser에 대한 정보와 Datanode의 Capacity 확인
> 

## Comment
- Dataproc은 서버리스가 아니기 떄문에 몇개의 노드를 쓸것인지, 어떤 사양으로 쓸 것인지, SSD는 쓸지 말지를 직접 결정해야한다.
- 노드나 클러스터에 tag를 붙여서 방화벽을 관리하면 노드나 클러스터를 삭제했다가 다시 생성해서 IP가 변경되는 경우에도 동일한 방화벽 정책을 유지할 수 있다.
- 클러스터에 workflow 메뉴가 있는데 어떻게 작성하는 것인지 궁금함
> from Qwik Lab.
~~~
Pranay Khilari (Qwiklabs Support)

Jan 6, 08:24 EST

Hi Goun,

Greetings!

The Cloud Dataproc workflow provides a flexible and easy-to-use mechanism for managing and executing workflows. A Workflow Template is a reusable workflow configuration. It defines a graph of jobs with information on where to run those jobs.

Oozie is integrated with the rest of the Hadoop stack supporting several types of Hadoop jobs out of the box (such as Java map-reduce, Streaming map-reduce, Pig, Hive, Sqoop and Distcp) as well as system specific jobs (such as Java programs and shell scripts). Currently, we do not have any specific lab related to this.

Also, we would like to tell you that the lab supports HIVE CLI only. Each lab is designed for purpose to learn specific function. Beeline was developed specifically to interact with the new server. Unlike Hive CLI, which is an Apache Thrift-based client, Beeline is a JDBC client based on the SQLLine CLI — although the JDBC driver used communicates with HiveServer.

Let us know if we can help you with anything else.

See you in the Cloud,
Pranay from Qwiklabs.
~~~
