# Detect Labels, Faces, and Landmarks in Images with the Cloud Vision API
- Levels: advanced
- Link: https://www.qwiklabs.com/catalog_lab/1112

## Summary
- Cloud bucket으로 이미지를 올려서 Vision API를 호출하는 예제
- 동일한 URI를 가지고 <request.json>에 `LABEL_DETECTION` 또는 `WEB_DETECTION` 을 바꿔서 사용하면 다른 결과물을 가져오는 것을 확인
~~~bash
curl -s -X POST -H "Content-Type: application/json" --data-binary @request.json  https://vision.googleapis.com/v1/images:annotate?key=${API_KEY}
~~~
> 참고: curl 옵션 설명 <br>
> -s : silent mode <br>
> -X : 요청 메소드를 지정 (기본값은 GET) <br>
> -H : 요청 헤더를 지정 <br>
- Keywords: `API` `LABEL_DETECTION` `WEB_DETECTION` `FACE_DETECTION` `LANDMARK_DETECTION`

## Prep
- API & Services > Credentials 메뉴에서 key를 생성
- 버켓에 이미지를 올릴 때 AllUsers Group에 Reader 권한을 주어 public link가 생기도록 해야함

## Label Detection & Web Detection
- request.json
~~~json
{
  "requests": [
      {
        "image": {
          "source": {
              "gcsImageUri": "gs://my-bucket-name-kk/donut.jpg"
          }
        },
        "features": [
          {
            "type": "LABEL_DETECTION",
            "maxResults": 10
          }
        ]
      }
  ]
}
~~~
- response
~~~json
{
  "responses": [
    {
      "labelAnnotations": [
        {
          "mid": "/m/01dk8s",
          "description": "powdered sugar",
          "score": 0.9436922,
          "topicality": 0.9436922
        },
        {
          "mid": "/m/01wydv",
          "description": "beignet",
          "score": 0.7160288,
          "topicality": 0.7160288
        },
        {
          "mid": "/m/06_dn",
          "description": "snow",
          "score": 0.7121922,
          "topicality": 0.7121922
        },
        {
          "mid": "/m/0bp3f6m",
          "description": "fried food",
          "score": 0.70753103,
          "topicality": 0.70753103
        },
        {
          "mid": "/m/052lwg6",
          "description": "baked goods",
          "score": 0.53270763,
          "topicality": 0.53270763
        }
      ]
    }
  ]
}
~~~
- request.json에 LABEL_DETECTION을 WEB_DETECTION으로 바꾸면 유사한 이미지를 얻을 수 있음

## Face and Landmark Detection


## Trouble Shooting
- Invalid JSON payload: Request 시 오류. Copy & Paste하다가 앞 부분이 잘려서 request.json을 수정
~~~json
{
  "error": {
    "code": 400,
    "message": "Invalid JSON payload received. Unexpected token.\nts\": [\n      {\n     \n^",
    "status": "INVALID_ARGUMENT"
  }
}
- bucket명은 내가 생성한 bucket명이어야 함. 정확한 URL을 가져오기 위해서는 Overview tab에 Link for gsutil에서 복사
~~~json
{
  "responses": [
    {
      "error": {
        "code": 7,
        "message": "Error opening file: gs://my-bucket-name/donuts.png."
      }
    }
  ]
}

$ cat request.json # request가 잘려있음
ts": [
      {
        "image":
~~~

## Comment
- request.json에 대한 포맷은 어디에 적혀있나?