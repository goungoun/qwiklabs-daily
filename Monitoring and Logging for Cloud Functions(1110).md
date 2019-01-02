# Monitoring and Logging for Cloud Functions
- Levels: fundamental
- Link: https://www.qwiklabs.com/catalog_lab/1110

# Summary
- Cloud function을 만들고 Vegeta라는 도구를 사용하여 인위적으로 트래픽을 생성한 후 실행시간, 실행 카운트, 메모리 사용률 모니터링해본다.
- cloud function에서도 간단한 메트릭을 볼 수 있지만 stackdriver monitoring에서는 alert도 가능하도록 되어있다.

## Cloud Functions
- 소스
~~~
exports.qwiklabsDemo = function qwiklabsDemo (req, res) {
  res.send(`Hello ${req.body.name || 'World'}!`);
  console.log(req.body.name);
}
~~~
> 생각보다 clou봄 function이 만들어지는 속도가 빠르지가 않고 체감상 cloud function이 생성되는 속도가 VM 만드는 속도나 kubernetes cluster가 만들어지는 속도와 비슷한것 같은 느낌은?
- testing tab에서 기능정도는 테스트 가능하지만 대규모 트래픽은 못 만듬
~~~json
{"name":"Gounna"}
~~~
> output: Hello Gounna!

## Vegeta
- Vegeta is a versatile HTTP load testing tool built out of a need to drill HTTP services with a constant request rate
> test traffic을 인위적으로 생성해주는 도구
~~~
wget 'https://github.com/tsenart/vegeta/releases/download/v6.3.0/vegeta-v6.3.0-linux-386.tar.gz'
tar xvzf vegeta-v6.3.0-linux-386.tar.gz
~~~
- Cloud Functions의 Trigger 탭에서 trigger url을 얻어 스크립트를 실행하는 순간 Cloud function의 invocation 그래프가 쫙 올라간다
> Stack driver쪽에 로그도 많이 찍힌다
echo "GET https://us-central1-qwiklabs-gcp-11ffe547d0446ad0.cloudfunctions.net/qwiklabsDemo" | ./vegeta attack -duration=300s > results.bin

## Stack driver logging
- 로그 확인 및 모니터링 대시보드
> 모든 서비스에 대한 log가 stack driver로 미리 통합되어있다는 것은 ELK를 써서 로그를 통합하지 않아도 되는 것인가? 왜냐하면 stackdriver에 로그가 통합되어있을 뿐만 아니라 사용자가 metric을 정의해서 대시보드 생성하는 기능까지 다 갖추고 있음

## Comment
- Stack driver를 쓰려면 원래 project명으로 project를 만들어줘야했던가?
- Stack driver project 설정의 제일 마지막에 VM에 agent를 설치하는 가이드가 있는데 기본으로 설치되어있지는 않은 것인지?
~~~bash
# To install the Stackdriver monitoring agent:
$ curl -sSO https://dl.google.com/cloudagents/install-monitoring-agent.sh
$ sudo bash install-monitoring-agent.sh

# To install the Stackdriver logging agent:
$ curl -sSO https://dl.google.com/cloudagents/install-logging-agent.sh
$ sudo bash install-logging-agent.sh
~~~
- 잘못 만들면 수정이 안됨. (Dataproc이나 cron에서 그랬던 것 처럼 수정 불가가 기본)
