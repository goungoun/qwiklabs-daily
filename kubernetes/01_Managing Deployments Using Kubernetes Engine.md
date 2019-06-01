# Managing Deployments Using Kubernetes Engine
> https://github.com/googlecodelabs/orchestrate-with-kubernetes.git

## Keyword
Continuous Deployment, Blue-Green Deployments, Canary Deployments
Heterogeneous deployments
session affinity

## Summary
- Create a Kubernetes cluster and deployments (Auth, Hello, and Frontend)
~~~bash
# Create cluster
gcloud container clusters create bootcamp --num-nodes 5 --scopes "https://www.googleapis.com/auth/projecthosting,storage-rw"

# Kubectl explain deployment
kubectl explain deployment --recursive
kubectl explain deployment.metadata.name

## Deployment manifest
kubectl create -f deployments/auth.yaml

# Once the deployment is created, Kubernetes will create a ReplicaSet for the Deployment. 
kubectl get deployments
kubectl get replicasets
kubectl get pods

# create a service for our auth deployment
kubectl create -f services/auth.yaml
kubectl create -f deployments/hello.yaml
kubectl create -f services/hello.yaml

# expose the frontend deployment
kubectl create secret generic tls-certs --from-file tls/
kubectl create configmap nginx-frontend-conf --from-file=nginx/frontend.conf
kubectl create -f deployments/frontend.yaml
kubectl create -f services/frontend.yaml

kubectl get services frontend

# curl -ks https://35.222.98.239:443 
curl -ks https://`kubectl get svc frontend -o=jsonpath="{.status.loadBalancer.ingress[0].ip}"`

# Scale a Deployment
kubectl explain deployment.spec.replicas
kubectl scale deployment hello --replicas=5
kubectl get pods | grep hello- | wc -l
kubectl scale deployment hello --replicas=3
kubectl get pods | grep hello- | wc -l

# Rolling Update하기 위해 버전을 1에서 2로 고치면 자동으로 roll out됨
kubectl edit deployment hello 
kubectl get replicaset
kubectl rollout history deployment/hello

# 문제가 생겨 rollout을 멈추었다가 재개 또는 undo
kubectl rollout pause deployment/hello
kubectl rollout resume deployment/hello
kubectl rollout undo deployment/hello

# rollout 상태 살펴보기
kubectl rollout status deployment/hello
kubectl get pods -o jsonpath --template='{range .items[*]}{.metadata.name}{"\t"}{"\t"}{.spec.containers[0].image}{"\n"}{end}'
auth-6bb8dcd7bd-9lwkc           kelseyhightower/auth:1.0.0
frontend-77f46bf858-k9g2s               nginx:1.9.14
hello-5cbf94fc49-4g8cs          kelseyhightower/hello:1.0.0
hello-5cbf94fc49-5kxvz          kelseyhightower/hello:1.0.0
hello-677685c76-dnnps           kelseyhightower/hello:2.0.0
hello-677685c76-pw7mn           kelseyhightower/hello:2.0.0
hello-677685c76-wfwc4           kelseyhightower/hello:2.0.0
hello-677685c76-wgr6c           kelseyhightower/hello:2.0.0

# 카나리 배포
kubectl create -f deployments/hello-canary.yaml
curl -ks https://`kubectl get svc frontend -o=jsonpath="{.status.loadBalancer.ingress[0].ip}"`/version

# 블루 그린 배포 후 서비스를 블루에서 그린 클러스터로 전환
kubectl create -f deployments/hello-green.yaml
kubectl apply -f services/hello-green.yaml
~~~
> kubectl explain deployment --scope option을 주면 sub element까지 리스트됨
~~~yaml
FIELDS:
   apiVersion   <string>
     APIVersion defines the versioned schema of this representation of an
     object. Servers should convert recognized schemas to the latest internal
     value, and may reject unrecognized values. More info:
     https://git.k8s.io/community/contributors/devel/api-conventions.md#resources
   kind <string>
     Kind is a string value representing the REST resource this object
     represents. Servers may infer this from the endpoint the client submits
     requests to. Cannot be updated. In CamelCase. More info:
     https://git.k8s.io/community/contributors/devel/api-conventions.md#types-kinds
   metadata     <Object>
     Standard object metadata.
   spec <Object>
     Specification of the desired behavior of the Deployment.
   status       <Object>
     Most recently observed status of the Deployment.
~~~

~~~yaml
FIELDS:
   apiVersion   <string>
   kind <string>
   metadata     <Object>
      annotations       <map[string]string>
      clusterName       <string>
      creationTimestamp <string>
      deletionGracePeriodSeconds        <integer>
      deletionTimestamp <string>
      finalizers        <[]string>
      generateName      <string>
      generation        <integer>
      initializers      <Object>
         pending        <[]Object>
            name        <string>
         result <Object>
            apiVersion  <string>
            code        <integer>
            details     <Object>
               causes   <[]Object>
                  field <string>
                  message       <string>
~~~

### Rolling Update
> 천천히 과거 버전의 replicaset을 내리고 새 버전으로 업데이트 하는 과정 <br>
> When a Deployment is updated with a new version, it creates a new ReplicaSet and slowly increases the number of replicas in the new ReplicaSet as it decreases the replicas in the old ReplicaSet.
- deployments/hello.yaml 
~~~yaml
spec:
  replicas: 3
  template:
    metadata:
      labels:
        app: hello
        track: stable
        version: 1.0.0
~~~
`spec.template.metadata.labels.track=stable`
> https://github.com/googlecodelabs/orchestrate-with-kubernetes/blob/master/kubernetes/deployments/hello.yaml

### Canary Deployment
- 새 버전의 일부만 배포해보는 배포 방법
- 사용자가 서비스를 사용하던 중에 배포가 되어버리면 사용자가 당황할 것이다. 다음 페이지로 넘어갔는데 디자인이 확 바뀌어버리거나 하는 등의 동작을 방지하기 위해서 service쪽에 `spec.sessionAffinity: ClientIP` 을 넣어주면 세션을 유지하는 동안은 동일한 버전으로 서비스 할 수 있음
- deployments/hello-canary.yaml
~~~yaml
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: hello
        track: canary
        version: 2.0.0
~~~
> https://github.com/googlecodelabs/orchestrate-with-kubernetes/blob/master/kubernetes/deployments/hello-canary.yaml


### Blue-green Deployment
- 다른 이름으로 배포를 하고 문제가 없으면 서비스를 새로 배포한 쪽으로 돌려주는 방법

## Comment
> 배포 절차 자체를 yaml로 기술해서 관리하는 접근 방식이 좋은 것 같음. 배포가 진행되는 과정을 UI로 확인하려면 무엇을 연동해야 할까?