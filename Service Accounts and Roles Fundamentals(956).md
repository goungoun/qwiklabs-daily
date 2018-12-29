# Service Accounts and Roles: Fundamentals
- Levels: fundamental
- Link: https://www.qwiklabs.com/catalog_lab/956

## Summary
- service account으로 `bigquery-qwiklab`을 생성하고 Big query 데이터를 볼 수 있는 권한을 부여
- Compute Engine 메뉴에서 vm 만들 때 계정을 연결해서 생성
- 만든 service account로 Bigquery에 접속하여 데이터를 가져오는 간단한 python 코드 예제 실행
- keyword: `service account`

## Service Account
- 서비스 계정은 개별 end user가 아니라 application이 사용하는 계정임
~~~
gcloud iam service-accounts create my-sa-123 --display-name "my service account"
~~~
- 서비스 바인딩
~~~
gcloud projects add-iam-policy-binding $DEVSHELL_PROJECT_ID \
    --member serviceAccount:my-sa-123@$DEVSHELL_PROJECT_ID.iam.gserviceaccount.com --role roles/editor
~~~
> 위 작업은 모두 IAM UI를 통해서 생성 가능

## Sample Code
- 설치 
~~~
sudo pip install google-cloud-bigquery
~~~
- Sample Code
~~~py
from google.auth import compute_engine
from google.cloud import bigquery

credentials = compute_engine.Credentials(
    service_account_email='YOUR_SERVICE_ACCOUNT')

query = '''
SELECT
  year,
  COUNT(1) as num_babies
FROM
  publicdata.samples.natality
WHERE
  year > 2000
GROUP BY
  year
'''

client = bigquery.Client(
    project='YOUR_PROJECT_ID',
    credentials=credentials)
    
print(client.query(query).to_dataframe())
~~~

## Comment
- 우리 회사 machine 계정인 ps계정이 이 체계와 비슷
- vm에 service account 계정을 붙이는 것이 어떤 의미인가요? 해당 VM에서 접근 가능한 리소스를 통제하겠다는 의미인가요?
> 퀵랩 초반부에 잘 설명되어 있음 
~~~
A service account is a special Google account that belongs to your application or a virtual machine (VM) instead of an individual end user. Your application uses the service account to call the Google API of a service, so that the users aren't directly involved.

For example, a Compute Engine VM may run as a service account, and that account can be given permissions to access the resources it needs. This way the service account is the identity of the service, and the service account's permissions control which resources the service can access.

A service account is identified by its email address, which is unique to the account.
~~~
- 그렇다면 위에 생성된 계정으로 붙어있는 VM에서는 BigQuery외에 GCP의 다른 서비스를 사용할 권한이 없음. 해당 VM에서 BigTable을 사용하고 싶은 경우는 service account쪽에 권한을 줘야 함
- service account는 패스워드도 없고 GCP에 login하는 계정이 아닌데 어떻게 인증이 되는 것인지? VM에 접속할때도 password를 물어보지 않는데?