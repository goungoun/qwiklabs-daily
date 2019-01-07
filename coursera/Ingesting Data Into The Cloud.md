# Ingesting Data Into The Cloud
- Levels: Fundamental
- Link: https://www.qwiklabs.com/catalog_lab/994
- GitHub: https://github.com/GoogleCloudPlatform/data-science-on-gcp/

## Summary
- BTS로부터 데이터를 다운로드 받아서 데이터를 처리하기 쉽게 변환하기 위해 셸 스크립트를 만들고 클라우드 버켓에 올리는 실습
- KeyWords: `gsutil mb` `gsutil -m cp`

## Cheat Sheet
- bash
~~~bash
seq -w 1 12
head -2 201503.csv
sed 's/,/ /g' # wc -l을 하기 위해 ,를 space로 변경
sed 's/,$//g' # 줄 끝에 , 제거
wc -w # space count
curl [url] --output renamed_file.zip
${parameter:=word}
~~~
- gsutil
~~~
export PROJECT_ID=$(gcloud info --format='value(config.project)')
gsutil mb gs://${PROJECT_ID}-ml
gsutil -m cp *.csv gs://${PROJECT_ID}-ml/flights/raw/
~~~
> gsutil 퀵 스타트 : https://cloud.google.com/storage/docs/quickstart-gsutil?hl=ko

## On-Time performance data
~~~bash
curl https://www.bts.dot.gov/sites/bts.dot.gov/files/docs/legacy/additional-attachment-files/ONTIME.TD.201501.REL02.04APR2015.zip --output data.zip
~~~
> --out 옵션을 주면 원하는 파일명으로 변경해서 저장할 수 있음

## Upload to Bucket
- 파일명 전처리: 201501.csv ~ 201512.csv
~~~
for month in `seq -w 1 12`; do
   unzip 2015$month.zip
   mv *ONTIME.csv 2015$month.csv
   rm 2015$month.zip
done
~~~
> 압축 파일만 다르고 그 안에 같은 이름으로 파일이 들어가있어서 mv명령어로 파일명을 바꿔줌. 압축 해제후 원 zip 파일은 해제
- 컬럼 수 확인하기
~~~
head -2 201503.csv  | tail -1 | sed 's/,/ /g' | wc -w
~~~

- head 명령어로 보면 각 파일마다 헤더가 있는데 어떻게 처리하나? 
~~~
head -2 201503.csv  | tail -1 | sed 's/,/ /g' | wc -w
~~~
> ,를 공백으로 바꿔서 wc -w를 사용
> 그런데 왜 두줄 가져오는지는 모르겠음 `head -1 201503.csv | sed 's/,/ /g' | wc -w`도 같은 결과
- Cloud storage bucket에 올리기
~~~
gsutil -m cp *.csv gs://$BUCKET/flights/raw
~~~

## Comment
- 버켓 만드는 것 정도는 gcloud command를 외울 수 있으면 좋겠다.
- [Stack Overflow: Bash script what is :=for?](https://stackoverflow.com/questions/1064280/bash-script-what-is-for)
~~~bash
${parameter:=word}

파라미터가 비어있으면 word로 대입해주고 그렇지 않으면 기존에 설정된 파라미터를 그대로 사용 

export BUCKET=${BUCKET:=cloud-training-demos-ml}

여기서 사용된 예제는 환경변수에 BUCKET이 설정되어있으면 그것을 그대로 쓰고 없는 경우 자동으로 cloud-training-demos-ml로 세팅됨
~~~