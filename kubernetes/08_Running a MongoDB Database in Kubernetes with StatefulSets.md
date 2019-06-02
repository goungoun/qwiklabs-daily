# Running a MongoDB Database in Kubernetes with StatefulSets

> https://github.com/goungoun/mongo-k8s-sidecar/tree/master/example/StatefulSet

## Keyword
`mongodb`, `statefulSet` <br>
`StorageClass`, `Headless service`, `PersistentVolume` <br>
`terminationGracePeriodSeconds`, `volumeClaimTemplates` <br>
`headless service`

## Summary
- HA 구성과 데이터를 중복 저장하기 위해 replica set을 사용 
~~~bash
# hello-world 클러스터 생성
gcloud config set compute/zone us-central1-f
gcloud container clusters create hello-world

# 몽고 DB와의 통합
git clone https://github.com/thesandlord/mongo-k8s-sidecar.git
cd ./mongo-k8s-sidecar/example/StatefulSet/

# Instantiate a StorageClass, headless service, and StatefulSet
kubectl apply -f googlecloud_ssd.yaml
kubectl apply -f mongo-statefulset.yaml

kubectl get statefulset
kubectl get pods

# 몽고 DB 접속
kubectl exec -ti mongo-0 mongo

# 접속 후 몽고 DB 커맨드 테스트
# rs.initiate()
# rs.conf()

# Scale up and down
kubectl scale --replicas=5 statefulset mongo
kubectl scale --replicas=3 statefulset mongo

# Clean up
kubectl delete statefulset mongo
kubectl delete svc mongo
kubectl delete pvc -l role=mongo
gcloud container clusters delete "hello-world"
~~~
- ./example/StatefulSet/googlecloud_ssd.yaml
~~~yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1beta1
metadata:
  name: fast
provisioner: kubernetes.io/gce-pd
parameters:
  type: pd-ssd
~~~
- ./example/StatefulSet/mongo-statefulset.yaml
> 이 예제의 핵심
~~~yaml
apiVersion: v1   <-----------   Headless Service configuration
kind: Service
metadata:
  name: mongo
  labels:
    name: mongo
spec:
  ports:
  - port: 27017
    targetPort: 27017
  clusterIP: None
  selector:
    role: mongo
---
apiVersion: apps/v1beta1    <------- StatefulSet configuration
kind: StatefulSet
metadata:
  name: mongo
spec:
  serviceName: "mongo"
  replicas: 3
  template:
    metadata:
      labels:
        role: mongo
        environment: test
    spec:
      terminationGracePeriodSeconds: 10
      containers:
        - name: mongo
          image: mongo
          command:
            - mongod
            - "--replSet"
            - rs0
            - "--smallfiles"
            - "--noprealloc"
          ports:
            - containerPort: 27017
          volumeMounts:
            - name: mongo-persistent-storage
              mountPath: /data/db
        - name: mongo-sidecar
          image: cvallance/mongo-k8s-sidecar
          env:
            - name: MONGO_SIDECAR_POD_LABELS
              value: "role=mongo,environment=test"
  volumeClaimTemplates:
  - metadata:
      name: mongo-persistent-storage
      annotations:
        volume.beta.kubernetes.io/storage-class: "fast"
    spec:
      accessModes: [ "ReadWriteOnce" ]
      resources:
        requests:
          storage: 100Gi
~~~

## Storage Class
- 데이터베이스 노드에서 쓸 저장소로 어떤 옵션을 쓸지를 기술하는 곳: 표준 영구 디스크, 리전 영구디스크, SSD 등
> https://cloud.google.com/compute/docs/disks/
- To describe the `classes` of storage
- Fields to dynamically provision : `provisioner`, `parameters`, and `reclaimPolicy`
> https://kubernetes.io/docs/concepts/storage/storage-classes/

## Headless service
- 데이타베이스와 같이 master,slave 구조가 있는 서비스들의 경우 쿠버네티스 service를 이용해서 로드밸런싱을 하는 것이 아니기 때문에, 로드밸런서의 역할은 필요가 없고 pod들을 묶어줄 수 있는 service만 있으면 되기 때문에 headless 서비스를 활용한다.
> https://bcho.tistory.com/tag/headless service [조대협의 블로그]

## Discussion
- statefulSet을 활용하는 예제로 몽고디비를 선정
- 몽고 디비 자체도 분산 클러스터인데 kubernetes를 활용해서 셋업하면 무엇이 좋아지는지?
- 원 저자의 github repository로 가보면 PoC setup 예제로 소개되어있고 아직 몇가지의 셋업이나 구현이 todo로 남아있음. 현재는 active하지 않은 듯
