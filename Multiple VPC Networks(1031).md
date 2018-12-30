# Multiple VPC Networks
- Levels: advanced
- Link: https://google.qwiklabs.com/catalog_lab/1031

## Summary
- Create a VM instance with multiple network interfaces (nic0, nic1, nic2..)
- 새로 만든 VM에서 mynetwork, privanet, managementnet 내의 VM에 ping이 가는지 확인
![multiple_network_interfaces.png](./images/multiple_network_interfaces.png)
- keywords:`vpc` `gcloud compute networks [create|subnets]`
- 생성된 네트워크 확인
~~~
gcloud compute networks list
gcloud compute networks subnets list --sort-by=NETWORK
~~~
- vm-appliance에서의 `ip route`
~~~
google1968386_student@vm-appliance:~$ ip route
default via 172.16.0.1 dev eth0 
10.128.0.0/20 via 10.128.0.1 dev eth2 
10.128.0.1 dev eth2 scope link 
10.130.0.0/20 via 10.130.0.1 dev eth1 
10.130.0.1 dev eth1 scope link 
172.16.0.0/24 via 172.16.0.1 dev eth0 
172.16.0.1 dev eth0 scope link 
~~~

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
> subnet이 auto인 경우 각 region마다 subnet이 나오지만 `--subnet-mode=custom` 으로 만든 경우는 직접 만든 subnet만 나옴

# Firewall
- 다른 VPC에 있는 VM인 경우 internal ip, external ip로 ping 불가
- 동일한 VPC 내에 있는 mynet-eu-vm와 mynet-us-vm는 internal ip로도 ping 가능
~~~
$ gcloud compute --project=qwiklabs-gcp-9ace8700a6f2b235 firewall-rules create managementnet-allow-icmp-ssh-rdp --direction=INGRESS --priority=1000 --network=default --action=ALLOW --rules=tcp:22,tcp:3389,icmp --source-ranges=0.0.0.0/0
$ gcloud compute firewall-rules list --sort-by=NETWORK
~~~
> icmp를 방화벽에서 허용해주면 external IP로만 ping이 가능

## VM with multiple network Interface
- VPC 네트워크 인스턴스는 기본 network interface를 가지고 있다
- 인스턴스 유형에 따라 허용 가능한 가상 NIC의 갯수가 다른데 n1-standard의 경우는 최대 8개까지 추가 가능 <br>
https://cloud.google.com/vpc/docs/create-use-multiple-interfaces#max-interfaces
~~~bash
ping -c 3 <Enter privatenet-us-vm's internal IP here>
ping -c 3 privatenet-us-vm
ping -c 3 <Enter managementnet-us-vm's internal IP here>
ping -c 3 <Enter mynet-us-vm's internal IP here>
ping -c 3 <Enter mynet-eu-vm's internal IP here>
~~~

## Comment
- 처음 해 볼때는 UI가 편하고 이 예제와 같이 여러개를 한꺼번에 만들어야 하는 경우는 script를 사용하는 것이 편리
- 여기에서 쓰는 VM은 ephemeral IP 라서 restart하면 ip가 바뀌는데 이 실습에서와 같은 firewall rule을 적용하면  internal/external ip에 대한 ping의 응답 결과는 항상 같을지 궁금
- VM을 여러개 만들어보았지만 숨어있는 공간에 여러 network를 연결할 수 있는 공간이 있는 것은 몰랐음
