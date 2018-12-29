# VPC Networking Fundamentals
- Levels: fundamental
- Link: https://google.qwiklabs.com/catalog_lab/1030

## Summary
- 데이터 센터 네트워크와 같은 실제 네트워크의 가상 버전인 VPC를 만들어서 여러 국가에 걸친 REGION을 내부 네트워크로 엮어본다.
- 기본으로 만들어진 VPC 네트워크를 다 지우고 Route가 비어있는 것을 확인, VM도 생성 불가능한 것을 확인함으로써 VPC의 중요성을 인지
- VPC를 새로 생성하고 VM을 미국에 1개, 유럽에 1개를 만들고 할당되는 internal IP가 해당 region에 적용된 ip range에 해당하는지 확인
- 다른 Region에 생성된 VM에서 내부 IP로 ping을 날려서 연결되어있는지를 확인
- 설정된 firewall을 변경한 후 다시 ping을 날려보는 실험
- Keyword: `VPC` `subnets` `WAN` `ingress` `egress` `RFC 1918 CIDR`

## VPC
- VPC란 GCP에 의해 가상화되어 관리한다는 점 빼고는 물리적인 네트워크와 개념상으로 같음
- 데이터 센터의 가상 subnet을 구성하고 Global WAN(wide area network)으로 연결

## Firewall rules
- Action: Allow/Deny
- Ingress: mynetwork-allow-icmp, mynetwork-allow-innternal, mynetwork-allow-rdp, mynetwork-allow-ssh 네 개를 모두 선택하면 default와 같고 mynetwork-allow-all-ingress 옵션도 존재
- Egress: mynetwork-allow-all-egress
> AWS에서 VPC 세팅할 때는 network class range를 따져가면서 수동으로 세팅했었는데 이 Lab에서는 automatic 옵션을 선택하면 자동으로 설정 가능
- allow-internal을 지우는 순간 잘 가던 내부 ip ping이 안됨
~~~
$ ping -c 3 10.128.0.2
PING 10.128.0.2 (10.128.0.2) 56(84) bytes of data.

--- 10.128.0.2 ping statistics ---
3 packets transmitted, 0 received, 100% packet loss, time 2052ms
~~~
> allow-ICMP가 ping을 막을 것이라는 생각에 ICMP를 지워봤는데 VPC 내부에 있는 VM끼리는 ping이 잘 됨. 외부로 부터 들어오는 ping을 막는 용도일 듯 함
- You are able to SSH because of the allow-ssh firewall rule, which allows incoming traffic from anywhere (0.0.0.0/0) for tcp:22
> VPC를 생성하지 않고 VM을 만드는 경우에도 프로젝트 레벨에 기본 네트워크가 세팅이 되어있음
- Without a VPC network you cannot create VM instances, containers or App Engine applications. Therefore, each GCP project has a default network to get you started
- VM 생성 시에 아무 설정도 하지 않았는데 SSH로 접속이 가능했던 이유는 기본으로 Firewall 설정이 SSH 허용으로 세팅되어있기 때문인 것을 VPC firewall 메뉴에서 확인

## Comment
- 이번 Lab에서 기 생성된 VPC를 여러번 날려보는 실습을 해보고 나서야 드디어 VPC의 개념을 이해함 
- 실습환경으로 지웠다 만들었다 해볼 수 있어서 너무 감사합니다
- 자동으로 네트워크를 하나 만들면 각 Region마다 1개씩 만들어져서 총 18개나 생기는데 많지만 편하기는 하다 (하지만 회사에서 쓸 때는 정책을 잘 결정해야할듯)





