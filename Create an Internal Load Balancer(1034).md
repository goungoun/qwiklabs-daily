# Create an Internal Load Balancer
- Levels: advanced
- Permalink: https://www.qwiklabs.com/catalog_lab/1034

## Summary
- 동일한 리전 안에 2개의 Instance group을 만들고 Loadbalancer를 그 앞에 두어서 client instance가 접속할 때 forwarding rule에 따라 Instance Group 1에 접속할 것인지 Instance Group 2에 접속할 것인지 결정하는 예제

## VPC
- default외에 my-internal-app로 us-central1에 설정된 서브 네트워크를 확인
- subnet-a in us-central-1a: (ip)10.10.20.0/24, (gateway)10.10.20.1
- subnet-b in us-central-1b: (ip)10.10.30.0/24, (gateway)10.10.30.1
> 서브넷이 동일한 리전에 있어야 이유는 로드밸런서가 regioinal service이기 때문이다. 하지만 동일한 리전에 있다고 하더라도 다른 zone에 셋업하면 zonal failure로부터 시스템을 보호할 수 있다.

## Firewall 설정
- app-allow-http: Load balencer의 backend가 80 포트로부터 트래픽을 받을 수 있도록 설정
- app-allow-health-check: 어떤 instance가 새로운 커넥션을 받을지 health check이 결정할 수 있도록 셋업. 
> IP range에 130.211.0.0/22와 35.191.0.0/16을 설정해주는데 그 이유는?

## Instance Templagte
- An instance template is an API resource that you can use to create VM instances and managed instance groups. 
- 두번 째 instance template을 만들 때는 첫번째 만든 것을 copy한 후 subnet-a를 subnet-b로 바꿔줌
> Network tags의 용도: HTTP와 Health Check 방화벽 적용
