# Multiple VPC Networks
- Levels: advanced
- Link: https://google.qwiklabs.com/catalog_lab/1031

## Summary
- Create a VM instance with multiple network interfaces (nic0, nic1, nic2..)
- 새로 만든 VM에서 mynetwork, privanet, managementnet 내의 VM에 ping이 가는지 확인
![multiple_network_interfaces.png](./images/multiple_network_interfaces.png)
- keywords:`vpc` `gcloud compute networks [create|subnets]`

## Network
- management 네트워크와 subnet 만들기
~~~bash
gcloud compute --project=qwiklabs-gcp-9ace8700a6f2b235 networks create managementnet --subnet-mode=custom

gcloud compute --project=qwiklabs-gcp-9ace8700a6f2b235 networks subnets create managementsubnet-us --network=managementnet --region=us-central1 --range=10.130.0.0/20
~~~
- private 네트워크를 만들고 us와 eu에 subnet 추가하기
~~~bash
$ export NETWORK_NAME="privatenet"
$ gcloud compute networks create ${NETWORK_NAME} --subnet-mode=custom
$ gcloud compute networks subnets create privatesubnet-us --network=${NETWORK_NAME} --region=us-central1 --range=172.16.0.0/24
$ gcloud compute networks subnets create privatesubnet-eu --network=${NETWORK_NAME} --region=europe-west1 --range=172.20.0.0/20
~~~
> project는 생략
-- 생성된 네트워크 확인
~~~bash
$ gcloud compute networks list
NAME           SUBNET_MODE  BGP_ROUTING_MODE  IPV4_RANGE  GATEWAY_IPV4
default        AUTO         REGIONAL
mynetwork      AUTO         REGIONAL
managementnet  CUSTOM       REGIONAL
privatenet     CUSTOM       REGIONAL
~~~
> Auto Mode 네트워크는 모든 region에 서브네트워크를 자동으로 만들기 때문에 18개나 생기지만 custom mode로 만들면 subnet이 없기 때문에 ip 대역을 지정하여 subnet을 만들어서 추가해줘야 함
~~~bash
$ gcloud compute networks subnets list --sort-by=NETWORK # 전체 서브넷을 리스트업
$ gcloud compute networks subnets list --sort-by=NETWORK|grep managementsubnet
managementsubnet-us  us-central1              managementnet  10.130.0.0/20
$ gcloud compute networks subnets list --sort-by=NETWORK|grep privatesubnet
privatesubnet-eu     europe-west1             privatenet     172.20.0.0/20
privatesubnet-us     us-central1              privatenet     172.16.0.0/24
~~~

# Firewall
~~~
$ gcloud compute --project=qwiklabs-gcp-9ace8700a6f2b235 firewall-rules create managementnet-allow-icmp-ssh-rdp --direction=INGRESS --priority=1000 --network=default --action=ALLOW --rules=tcp:22,tcp:3389,icmp --source-ranges=0.0.0.0/0
$ gcloud compute firewall-rules list --sort-by=NETWORK
~~~

## VM with multiple network Interface
- VPC 네트워크 인스턴스는 기본 network interface를 가지고 있다
- VM에 network interface를 최대 8개까지 추가 가능
~~~bash
ping -c 3 <Enter privatenet-us-vm's internal IP here>
ping -c 3 privatenet-us-vm
ping -c 3 <Enter managementnet-us-vm's internal IP here>
ping -c 3 <Enter mynet-us-vm's internal IP here>
ping -c 3 <Enter mynet-eu-vm's internal IP here>
~~~

## Comment
- 처음 해 볼때는 UI가 편하고 이 예제와 같이 여러개를 한꺼번에 만들어야 하는 경우는 script를 사용하는 것이 편리



