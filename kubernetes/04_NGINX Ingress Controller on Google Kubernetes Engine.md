# NGINX Ingress Controller on Google Kubernetes Engine

## Keyword
`ingress`, `ingress resource`, `Helm Chart` <br>
`region`, `zones` <br>
`zonal resource`, `regional resource` <br>

## Summary
> Ingress Resource에 인바운드 트래픽에 대한 규칙을 정의하여 Kubernetes deployment를 만들고 NGIX를 Ingress Controller를 적용하는 예제
- Kubernetes deployment with an Ingress Resource
- Ingress Resource: rules for the inbound traffic to reach Services  `hostnames + path (optional paths)`
- Ingress Controller: HTTP or L7 load balancer, NGINX
![./images/NGINX_Ingress_Controller.png](./images/NGINX_Ingress_Controller.png)
> https://kubernetes.io/docs/concepts/services-networking/ingress/

~~~bash
# nginx-tutorial 클러스터 생성
gcloud compute zones list
gcloud config set compute/zone us-central1-a
gcloud container clusters create nginx-tutorial --num-nodes 2

# Helm 설치
curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get > get_helm.sh
chmod 700 get_helm.sh
./get_helm.sh

## Tiller를 설치하고 cluster-admin 으로 cluster role binding
kubectl create serviceaccount --namespace kube-system tiller
kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller
kubectl patch deploy --namespace kube-system tiller-deploy -p '{"spec":{"template":{"spec":{"serviceAccount":"tiller"}}}}' 
helm init --service-account tiller --upgrade

# kube-system의 deployment 확인: heapster, kube-dns, kubernetes-dashboard,.. tiller-deploy 
kubectl get deployments -n kube-system

# gcr.io에서 hello-app을 가져다가 8080포트로 접근할 수 있도록 처리
kubectl run hello-app --image=gcr.io/google-samples/hello-app:1.0 --port=8080
kubectl expose deployment hello-app

# NGINX 설치하고 ingress-resource를 적용
helm install --name nginx-ingress stable/nginx-ingress --set rbac.create=true
kubectl get service nginx-ingress-controller
kubectl apply -f ingress-resource.yaml
kubectl get ingress ingress-resource

# default backend가 동작하는지를 확인하기 위함
http://35.224.155.224/hello
http://35.224.155.224/test
~~~

- ingress-resource.yaml
~~~yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: ingress-resource
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
spec:
  rules:
  - http:
      paths:
      - path: /hello
        backend:
          serviceName: hello-app
          servicePort: 8080
~~~

## Ingress
- Ingress, added in Kubernetes v1.1, exposes HTTP and HTTPS routes from outside the cluster to services within the cluster. Traffic routing is controlled by rules defined on the Ingress resource.
~~~bash
    internet
        |
   [ Ingress ]
   --|-----|--
   [ Services ]
~~~

## NGINX
- High performance web server—is a popular choice for an Ingress Controller 
- Websockets, SSL Services, Rewrites URI, Session Persistence (same request to same backend container), JWTs (JSON Web Tokens)

## Discussion
- Helm이 익숙하지 않다면 Helm Package Manager를 먼저 꼭 해볼것
> https://google.qwiklabs.com/focuses/1044?parent=catalog
- zone과 관련한 제약사항을 잘 알고 있어야 함
> persistent disk을 VM에 붙일 때 같은 zone내에 있어야 하며, <br>
> 고정 IP를 붙일 때에도 VM instance가 같은 zone내에 있어야 함.
- 리전을 만들 때 3개 이상의 zone이 들어가야 할까? 2개이면 충분할까?
- `kubectl expose deployment hello-app` 해서 서비스 통해 deployment를 expose 시켜줘야 외부에 오픈됨
