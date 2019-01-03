# VPC Network Peering
- Levels: advanced
- Link: https://www.qwiklabs.com/catalog_lab/935

## Summary
- 실습 프로젝트 ID가 2개가 주어짐
- project-A에 NetworkA를 project-B에 networkB를 만들고 양방향으로 피어링한 후 project-B에 있는 vm에서 project-A에 있는 vm의 internal ip로 ping을 보내보는 방법으로 테스트
![peer-ab](https://gcpstaging-qwiklab-website-prod.s3.amazonaws.com/bundles/assets/6b7982c2748b1c1898a0626316a78fe24823ec781a63ced6f427af4c947478ae.png)

## 네크워크 셋업 (준비작업)
- 1st project: 서브넷을 custom으로 네트워크를 생성한다. subnet에 IP range를 주고 VM을 만들고 SSH로 접속할 수 있도록 SSH와 icmp를 활성화한다.
~~~bash
gcloud config set project <PROJECT_ID2>
gcloud compute networks create network-a --subnet-mode custom
gcloud compute networks subnets create network-a-central --network network-a \
    --range 10.0.0.0/16 --region us-central1
gcloud compute instances create vm-a --zone us-central1-a --network network-a --subnet network-a-central
gcloud compute firewall-rules create network-a-fw --network network-a --allow tcp:22,icmp
~~~

- 2nd project:
~~~bash
gcloud config set project <project 2> # 두 번째 프로젝트 ID 
gcloud compute networks create network-b --subnet-mode custom
gcloud compute networks subnets create network-b-central --network network-b \
    --range 10.8.0.0/16 --region us-central1
gcloud compute instances create vm-b --zone us-central1-a --network network-b --subnet network-b-central
gcloud compute firewall-rules create network-b-fw --network network-b --allow tcp:22,icmp
~~~

## Peering (본 작업)
- VPC network > VPC peering에서 설정 
> project-A쪽만 peering하면 waiting 상태
- 스크립트 제공되지 않음 

## Comment
- 다른 project의 network를 peering하는 방법은 되게 간단한데 이를 테스트하기 위한 준비 작업에 시간이 더 많이 소요
- GCP project에 설정된 VPC만 peering이 가능한가? 아니면 AWS나 on-prem 네트워크도 peering이 가능한가?