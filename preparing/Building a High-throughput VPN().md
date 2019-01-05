# Building a High-throughput VPN

## Summary

## Network
- 기 구축된 네트워크가 있다고 가정. Lab을 위해서 두개의 network을 만들고 그중 하나는 on-preem network라고 치자

## Create VPN GateWay
~~~
gcloud compute target-vpn-gateways create on-prem-gw1 --network on-prem --region us-central1
gcloud compute target-vpn-gateways create cloud-gw1 --network cloud --region us-east1
~~~

## Creating a route-based VPN tunnel between local and GCP networks
~~~
gcloud compute addresses create cloud-gw1 --region us-east1
gcloud compute addresses create on-prem-gw1 --region us-central1
~~~

## Comment
- Peering도 타 project에 있는 network을 연결할 수 있었는데 VPN은 어떻게 다른가? on-prem network를 연결할 수 있는가 없는가의 차이인가?