# Cloud TPU: Qwik Start
- Levels: introductory
- Link: https://google.qwiklabs.com/catalog_lab/1469

## Summary
- tpu를 쓸 수 있는 VM 인스턴스를 만들고 tensorflow 코드를 실행해본다.

## CheatSheet
~~~bash
ctpu print-config
ctpu up  --zone us-central1-b
~~~

~~~python
from tensorflow.contrib import tpu
from tensorflow.contrib.cluster_resolver import TPUClusterResolver
tpu_computation = tpu.rewrite(axy_computation, inputs)
tpu_grpc_url = TPUClusterResolver(
    tpu=[os.environ['TPU_NAME']]).get_master()
~~~

## TPU 셋업
~~~bash
ctpu up  --zone us-central1-b
ctpu print-config
~~~
> ctpu up 커맨드를 실행하면 VM instance가 하나 생김. 여기에는 최신의 stable Tensorflow가 설치되어있음

- `ctpu print-config`로 tpu name을 받아서 VM에 접속하여 환경변수로 셋업 후 예제 코드 실행
~~~bash
export TPU_NAME="googleasl1721-student"
python cloud-tpu.py
~~~

## Trouble Shooting
- 환경변수를 지정해주지 않으면 발생하는 에러. 예제 코드에서 `os.environ`를 사용해서 TPU_NAME을 받아오기 때문에 코드를 직접 고치기 보다는 환경변수를 export한 후 실행하여 fix
~~~
Traceback (most recent call last):
  File "cloud-tpu.py", line 18, in <module>
    tpu=[os.environ['TPU_NAME']]).get_master()
  File "/usr/lib/python2.7/UserDict.py", line 40, in __getitem__
    raise KeyError(key)
KeyError: 'TPU_NAME'
~~~


## Comment
- 생성된 VM을 보면 n1-standard-2 (2 vCPUs, 7.5 GB memory)라서 뭔가 고성능의 장비는 아닌 것 같은데 Labels에 ctpu라고 적혀있음