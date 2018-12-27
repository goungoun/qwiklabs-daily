# Cloud IAM
||info||desc||
|Levels| introductory|
|Permalink| https://www.qwiklabs.com/catalog_lab/686|

## IAM
- Viewer: (이 권한을 주면 프로젝트를 볼 수는 있지만 모든 버튼이 비활성화 되어있음)
- Editor: +Viewer에 수정까지 할 수 있는 권한
- Owner: +Editor 역할 관리까지 할수 있는 권한
- Browser: Viewer하고 비슷한데 folder, 조직, IAM 정책도 볼 수 있는 권한

## 실습 1
> 프로젝트 레벨에서 권한 부여
- user1으로 로그인해서 username2에게 Browser권한을 부여
- user2으로 로그인해서 프로젝트를 볼 수는 있지만 버튼이 비 활성화 되어있는 것을 확인
- user1으로 로그인해서 user2에 부여된 Brower권한과 Viewer 권한을 제거
- user2으로 로그인해서 해당 프로젝트를 볼 수 없게 된 것을 확인

## 실습 2
> Storage 레벨에서의 권한 부여
- user1이 user2에게 Storage> Browser 권한을 부여
- user2는 프로젝트 레벨에서 버켓을 볼수는 없지만 gsutil을 사용한 ls는 가능
~~~bash
gsutil ls gs://gcp_test_sksdjdjsjs
gs://gcp_test_sksdjdjsjs/sample.txt
~~~ 