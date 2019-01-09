# Create ML datasets
- Levels: fundamental
- Link: https://googlecoursera.qwiklabs.com/focuses/21305?locale=en
- Datalab Maual: https://cloud.google.com/datalab/?hl=ko

## Summary
- cloud shell에서 datalab을 생성하고 git cloud하여 소스 받는 예제

## DataLab
- compute engine에서 실행되는 데이터 탐색 도구 (jupyter notebook)

## CheetSheet
~~~bash
datalab create dataengvm --zone us-central1-a
datalab connect dataengvm # 끊어지면 cloud shell에 안내되니 외울필요는 없다
!git clone https://github.com/GoogleCloudPlatform/training-data-analyst
~~~

## Create datalab
- dataengvm이라는 VM이 만들어
~~~bash
datalab create dataengvm --zone us-central1-a
~~~
> 명령어 실행 후 Compute Engine쪽에 가서 확인해보면 dataengvm라는 이름으로 VM instance가 하나 만들어져있는 것을 확인함

## Comment
- 결국은 datalab이 설치되어있는 이미지를 가지고 VM instance를 만들어주는 것인가?
> 궁금해서 VM instance쪽에 살펴보니 gcr.io에서 docker image를 가져다가 설치된 것을 확인
- Datalab 설치에 5분 이상 걸리는데 일반 VM 생성이나 Dataproc 클러스터 또는 Kubernetes 시간보다 오래 걸리는 이유는?  Google BigQuery, Cloud Machine Learning Engine, Google Compute Engine, Google Cloud Storage에 대한 연결정보 설정이 되어있어서 그런 것인가? 특히 SSH propage할 때 시간이 오래 걸리는데 어디에 key를 propagate하는 것인지? Datalab가 scalability나 HA가 관리되는 분석 환경인지?
> 아마도 확장 가능하거나 HA까지는 아니라고 생각하는데 VM instance가 1개만 만들어지기 때문. key는 cloud shell쪽에서 만들어서 VM instance의 authorized_key 파일에 묻어두는 구조로 되어있다.
- python 분석 관련 모듈을 미리 install 해두는지?
> 아마도 docker image에...
- 설치 되어있는 module은 무엇이 있는지? 원하는 모듈을 설치하면 datalab에 유지가 되는지?
- 가격: datalab 자체는 무료로 제공되나 컴퓨팅, 저장, 기타 클라우드 서비스 비용이 발생할 수 있다고 하는데 Lab에서 보면 VM이 하나 새로 생기기 때문에 이 VM이 과금이 될 듯 함
- VM을 메모리가 괜찮은 VM에 셋업하려면 VM의 스펙을 설정할 수 있는 옵션이 있는지?
- Datalab의 경우 별도의 메뉴로 제공하지 않고 datalab create 커맨드로 설치하는데 그 이유는 구글이 만든 소프트웨어가 아니라 jupyter notebook을 가져다가 쓴 것이라서 그런것일까 하는 생각이 들었다