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
~~~
ctpu print-config
ctpu up  --zone us-central1-b
~~~
> ctpu up 커맨드를 실행하면 VM instance가 하나 생김. 여기에는 최신의 stable Tensorflow가 설치되어있음

## 예제코드
~~~python
import os
import tensorflow as tf
from tensorflow.contrib import tpu
from tensorflow.contrib.cluster_resolver import TPUClusterResolver

def axy_computation(a, x, y):
  return a * x + y

inputs = [
    3.0,
    tf.ones([3, 3], tf.float32),
    tf.ones([3, 3], tf.float32),
]

tpu_computation = tpu.rewrite(axy_computation, inputs)

tpu_grpc_url = TPUClusterResolver(
    tpu=[os.environ['TPU_NAME']]).get_master()

with tf.Session(tpu_grpc_url) as sess:
  sess.run(tpu.initialize_system())
  sess.run(tf.global_variables_initializer())
  output = sess.run(tpu_computation)
  print(output)
  sess.run(tpu.shutdown_system())

print('Done!')
~~~


## Comment
