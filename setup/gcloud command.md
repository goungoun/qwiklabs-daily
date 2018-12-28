# gcloud command

## Env setting
> https://google.qwiklabs.com/catalog_lab/1309
~~~
export PROJECT_ID=$(gcloud info --format='value(config.project)')
~~~

## ML Engine
> https://google.qwiklabs.com/catalog_lab/1309
~~~bash
gcloud ml-engine jobs submit training $JOBNAME \
  --module-name=trainer.task \
  --package-path=$(pwd)/flights/trainer \
  --job-dir=$OUTPUT_DIR \
  --staging-bucket=gs://$BUCKET \
  --region=$REGION \
  --scale-tier=STANDARD_1 \
  --output_dir=$OUTPUT_DIR \
  --traindata $DATA_DIR/train* --evaldata $DATA_DIR/test*
~~~
