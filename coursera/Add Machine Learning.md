# Add Machine Learning
- Coursera: Data Engineering on Google Cloud Platform
- Levels: fundamental
- Link: [https://googlecoursera.qwiklabs.com/catalog_lab/1362](https://googlecoursera.qwiklabs.com/focuses/21301)
- Git: https://github.com/GoogleCloudPlatform/training-data-analyst/blob/master/courses/unstructured

# Add Machine Learning(ML)
- Levels: fundamental
- Link: https://googlecoursera.qwiklabs.com/catalog_lab/1279

## Summary
- 텍스트를 읽어서 각 라인별로 sentiment analysis를 pyspark으로 돌려본다.
- Google API를 호출

## CheetSheet
~~~
sed -i "s/your-api-key/$APIKEY/" *dataprocML.py
gsutil cp /training/road-not-taken.txt gs://$BUCKET/sampledata/road-not-taken.txt
~~~

## Prep
- 환경 변수
~~~
DEVSHELL_PROJECT_ID=[PROJECT_ID]
BUCKET=[BUCKET]
APIKEY=[APIKEY]
export DEVSHELL_PROJECT_ID
export BUCKET
export APIKEY
~~~
> 다른 실습은 export를 붙여서 환경 변수를 만드는데 여기서는 나중에 export를 해줌 
> 차이는 export를 했을 때만 shell에서 읽어올 수 있게 되는데 그 부분을 확인시켜 주고 싶었던 것 같음. 나도 간단한 배시스크립트를 짜서 확인해봄
- 위 환경변수를 .py에서 가져가다 쓰는데 [stagelabs.sh](https://github.com/GoogleCloudPlatform/training-data-analyst/blob/master/courses/unstructured/stagelabs.sh)가 알아서 replace 해줌
~~~bash
if [[ -v $BUCKET ]]; then
  echo "BUCKET environment variable not found"
  OKFLAG=0
fi

..
if [ OKFLAG==1 ]; then
    sed -i "s/your-api-key/$APIKEY/" *dataprocML.py
~~~

## Dataproc Cluster 생성
- Main python file에 bucket의 파일을 올린 01-dataprocML.py, 02-dataprocML.py, 03-dataprocML.py을 설정해준다.


## Trouble Shooting
- 첫번째 시도에서 400 error로 pyspark job이 죽었는데 아마도 API KEY를 생성하지 않고 service key를 복사해서 사용했기 때문일 것으로 추측함
- 01-dataprocML.py 구글 NLP를 REST API로 호출하는 부분
~~~python
def SentimentAnalysis(text):
    from googleapiclient.discovery import build
    lservice = build('language', 'v1beta1', developerKey=APIKEY)

    response = lservice.documents().analyzeSentiment(
        body={
            'document': {
                'type': 'PLAIN_TEXT',
                'content': text
            }
        }).execute()
    
    return response
~~~
> https://github.com/GoogleCloudPlatform/training-data-analyst/blob/master/courses/unstructured/01-dataprocML.py

## Comment
- Google API를 호출하는 부분에서 이 부분이 mapper에 해당하는 부분일듯
- 전체 book에 대해 처리하는 예제는 executor 수를 늘려주지 않아도 되는지? 모든 예제에서 num_executor 와 같은 파라미터를 입력하는 부분이 없다.
- 사내 클러스터에서는 구글 NLP를 호출하지 못하는데 public cloud에서는 구글 API를 사용해서 spark을 개발할 수 있는 점이 편리
- python 3개 코드는 spark으로도 한번 바꿔봐야겠음