# Introduction to APIs in Google
- Levels: introductory
- Link: https://run.qwiklabs.com/catalog_lab/1342

## Summary
- REST API를 사용하기 위한 기초적이고 전반적인 설명
- Keywords: `Endpoints` `REST` `RESTful` `JSON` `API` `API Explorer` `API Keys`

## API
- API란? <br>
> API (Application Programming Interface) is a software program that gives developers access to computing resources and data

## HTTP request method
- GET
- PUT: update or insert
- POST: insert
- DELETE

## Endpoint
- 서버 리소스나 데이터를 사용하기 위한 access point
- HTTP URI형식: API의 Base URL 뒤에 뭔가를 붙여서 사용
> 예시: http://example.com/storelocations, http://example.com/accounts

## Restful API
- Header와 Body로 구성
- Header: HTTP 요청 자체에 대한 상세 파라미터
- Body: Client가 서버로 보내는 데이터. Body는 XML또는 JSON을 사용

## JSON
- XML보다 가볍고 더 빨리 파싱이 되기 때문에 널리 사용됨
- 타입: Number, String, Boolean, Array, Null
> 숫자의 경우 integer나 floating point을 구별 없이 number로 사용
- JSON validator: [JsonLint](https://jsonlint.com/), [chrome Json View](https://chrome.google.com/webstore/detail/jsonview/chklaanhfefbnpoihckbnefhakgolnmc?hl=en)

## Authentication and Authorization
- Authentication: Process of determining a client's identity  (=Who you are)
- Authorization: Process of determining what permissions an authenticated client has for a set of resources(=What you do)
> Authenticated user란 신원이 확인된 유저이고 Authorization은 리소스 사용 권한을 확인하여 허가하는 작업으로 보면 됨
### API Keys
- 프로젝트에서 API 사용할 수 있도록 하는데 사용

### OAuth
- User account와 identity와 연계하여 사용하는 키
- 유저마다 다른 권한 설정을 하는데 사용

### Service Account
- 개인 엔드유저가 아니라 VM이나 Application에 부여되는 계정

## Example
- Cloud storage에 JSON/REST API를 사용하여 업로드 하기
~~~bash
curl -X POST --data-binary @${OBJECT} \
    -H "Authorization: Bearer ${OAUTH2_TOKEN}" \
    -H "Content-Type: image/png" \
    "https://www.googleapis.com/upload/storage/v1/b/${BUCKET_NAME}/o?uploadType=media&name=demo-image"
~~~

## API 사이트
- [ANY API](https://any-api.com/): API 모음
- [API Explorer](https://developers.google.com/apis-explorer): 구글이 제공하는 모든 API 
- [GCP API Library]( https://console.cloud.google.com/apis/library):  200+ google API 제공 (문서, 설정 포함) 

## Comment
- Documnet같은 Lab인데 잘 정리가 되어있어서 유용했다