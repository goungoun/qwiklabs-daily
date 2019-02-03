# Introduction to Kubeflow on Google Kubernetes Engine
- Levels: advanced
- Link: https://www.qwiklabs.com/catalog_lab/933

## Summary
- keyword: `kssonet` `ks init` `ks generate` `ks apply gke`

## Overview
![kubeflow](./images/Introduction_to_Kubeflow_on_Google_Kubernetes_Engine.png)

## Enable Boost Mode
- Cloud shell에서 docker build를 더 빨리할 수 있도록 좋은 VM으로 할당 (재시작 필요)

## Create ksonnet project
- Kubernetes는 정적인 YAML file을 사용하는 반면 ksonnet은 OOP object에 가까운 추상화의 개념을 추가
- ksonnet is a generic tool used to deploy software to Kubernetes
~~~bash
ks init ksonnet-kubeflow # ksonet project를 init
cd ${HOME}/kubeflow-introduction/ksonnet-kubeflow
~~~

## Comment
- kubeflow를 아직 공부하지 않은 상태에서 Lab을 하기에는 난이도가 있음
- 많은 부분 command 자체를 이해하지 못한 상태에서 실습하였으므로 level이 낮은 것 부터 다시 연습할 필요 