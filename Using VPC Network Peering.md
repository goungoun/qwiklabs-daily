# Using VPC Network Peering

## Summary
- Peering을 한 후 internal ip로 접속되는지 확
인
- 삭제
> Deleting one side of the peering connection, terminates the overall peering connection.

## Why VPC Peering?
- Network Latency, 
- Security
- Cost
> external IP를 사용하는 경우 egress bandwith 기준으로 과금

## Comment
- 예전에 해봤던 VPC Network Peering과 유사한데 quest를 끝내려고 다시 실습함
- 만약 ip range가 겹치는 경우에는 어떻게 해결할 수 있나?
- 과금과 속도를 생각하면 peering할 수 있다면 peering해서 사용하는 것이 좋을 것 같다는 생각
- VM instance page에서 Network를 체크 (그런 것이 있는지도 처음 알았고 네트워크 외에도 Labels, vm 생성 시각 등도 확인 가능)
- RFC 1918 ?
