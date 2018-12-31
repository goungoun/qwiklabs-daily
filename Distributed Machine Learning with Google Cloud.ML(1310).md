# Distributed Machine Learning with Google Cloud ML
- Levels: advanced
- Link: https://www.qwiklabs.com/catalog_lab/1310

## Summary
- esimator를 `linear_model`-> `dnn_model`-> `wide_and_deep_model`로 차례로 바꿔가면서 학습시켜본다
- 차원 축소를 적용해본다
- Learning rate를 조절해본다
- Keyword: `deep neural network classifier` `dimensionality reduction` `learning rate` `BigQuery RESTful` 

## Dimensionality reduction
- one-hot 인코딩으로 만들어진 1000개의 sparse column을 10개로 줄여서 다루기 쉽도록 함
~~~python
# create_embed: create embeddings of the sparse columns
def create_embed(sparse_col):
    dim = 10 # default
    if hasattr(sparse_col, 'bucket_size'):
       nbins = sparse_col.bucket_size
       if nbins is not None:
          dim = 1 + int(round(np.log2(nbins)))
    return tflayers.embedding_column(sparse_col, dimension=dim)
~~~

## DNN model
- 차원 축소 후 tflearn.DNNClassifier 적용
~~~python
def dnn_model(output_dir):
    real, sparse = get_features()
    all = {}
    all.update(real)

    # create_embed: create embeddings of the sparse columns
    embed = {
       colname : create_embed(col) \
          for colname, col in sparse.items()
    }
    all.update(embed)

    estimator = tflearn.DNNClassifier(
         model_dir=output_dir,
         feature_columns=all.values(),
         hidden_units=[64, 16, 4])
    estimator = tf.contrib.estimator.add_metrics(estimator, my_rmse)
    return estimator
~~~

## ML Engine
- job submit
~~~bash
gcloud ml-engine jobs submit training $JOBNAME \
  --module-name=trainer.task \
  --package-path=$(pwd)/flights/trainer \
  --job-dir=$OUTPUT_DIR \
  --staging-bucket=gs://$BUCKET \
  --region=$REGION \
  --scale-tier=STANDARD_1 \
  --runtime-version=1.10 \
  -- \
  --output_dir=$OUTPUT_DIR \
  --traindata $DATA_DIR/train* --evaldata $DATA_DIR/test*
~~~

## Learning Rate

## Trouble Shooting
- job이 fail되어 다시 동일한 이름으로 돌릴 때 오류
~~~
ERROR: (gcloud.ml-engine.jobs.submit.training) Resource in project [qwiklabs-gcp-9566f8a62e57cc2e] is the subject of a conflict: Field: job.job_id Error: A job with this id already exists.
- '@type': type.googleapis.com/google.rpc.BadRequest
  fieldViolations:
  - description: A job with this id already exists.
    field: job.job_id
~~~
> 첫번째 실패: ML Engine 메뉴에서 fail 된 job을 rerun 할 수 없어서 처음부터 다시 재시작
> 두번째 실패: 해결하기 위해서는 $JOBNAME을 다시 주고 돌
려줌. 왜냐하면 점수 체크하는 로직이 dnn-*, wide-*와 같이 job 이름 중에 시작하는 이름으로 체크하도록 되어있기 때문
-  파라미터 관련 오류
~~~
InvalidArgumentError (see above for traceback): GCS path doesn't contain a bucket name: gs:///flights/chapter8/output/
   [[Node: matching_filenames/MatchingFiles = MatchingFiles[_device="/job:master/replica:0/task:0/device:CPU:0"](matching_filenames/MatchingFiles/pattern)]]
~~~
> 환경변수를 정확하게 다시 주고 재작업
~~~
export DATA_DIR=gs://${BUCKET}/flights/chapter8/output
echo $DATA_DIR
~~~


## Comment
- ml engine에서 환경변수를 사용하여 파라미터를 주는 부분에서 오류의 발생 가능성이 있기 때문에 JOB을 실행하기 전에 사용된 환경변수를 먼저 모두 체크하고 돌리는 것이 오류를 줄이는 방법임 (Sqoop 쓸 때의 악몽이..)
- 특히 SSH가 어떤 이슈에 의해 멈추거나 해서 다시 띄워서 작업할 때 환경변수를 다시 세팅해줘야 함. 이런 것은 보통 sh 파일을 만들어서 환경설정을 잡아주는데 qwikLab 특성상 실습용 코드라 중간에 잘못하면 실습자가 알아서 잘 다시 셋업해줘야함.
- ML Engine UI가 완성되지 않은 느낌을 받음. 모니터링 하는 용도 외에 JOB을 재실행, 삭제, 배치 등의 작업을 수행할 수 있는 기능이 없음
- 마지막에 ml model 버전 만들어주는 부분이 꽤 오래 걸림
- 예제코드가 이번 Lab도 Copy & Paste하면 들여쓰기가 안 맞음
 