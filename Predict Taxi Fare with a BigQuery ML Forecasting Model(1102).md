# Predict Taxi Fare with a BigQuery ML Forecasting Model
- Levels: advanced
- Link: https://google.qwiklabs.com/catalog_lab/1102
- https://google.qwiklabs.com/focuses/1797?parent=catalog&qlcampaign=1q-30days-806

## Summary
- 택시비를 예측하는 머신러닝 모델을 빅쿼리로 만들어보자
- Keyword: `bqml` `linear_reg`

## CheatSheet
~~~sql
TIMESTAMP_TRUNC(pickup_datetime, MONTH) month
CREATE MODEL taxi.tatifare_model OPTIONS ...
ml.PREDICT(MODEL `taxi.taxifare_model`...
~~~

## Feature selection
- Tolls Amount + Fare Amount = Total Fare (=Label)
- Days of Week
- Hour of Day
- Pick up address
- Drop off address
- Number of passengers

## BQML 모델 만들기
- 현재로서는 linear_reg(예측), logistic_reg(분류) 두 가지 옵션 중 하나를 선택할 수 있다.
~~~sql
CREATE or REPLACE MODEL taxi.taxifare_model
OPTIONS
  (model_type='linear_reg', labels=['total_fare']) AS
-- paste the previous training dataset query here
SQRT(mean_squared_error) AS rmse
~~~
> NN이나 decision tree 같은 것은 Tensorflow를 사용해야한다.
- 학습 시간은 5~10분 정도 소요된다고 쓰여있으나 2분 40초만에 끝남

## 모델 성능 평가
- RMSE 사용

## Predict
~~~sql
SELECT
*
FROM
  ml.PREDICT(MODEL `taxi.taxifare_model`,
~~~

## Comment
- 테이블을 생성하듯 모델도 CREATE 구문으로 생성하도록 한 부분과 OPTIONS에 label과 model_type을 고를 수 있도록 해놓은 문법이 상당히 인상적
- BQML이 쉽게 ML을 작성하라고 만든 문법이겠지만 SQL 특성상 Subquery를 사용하기 시작하면 괄호 안의 괄호나 코드를 읽는 순서나 가독성이 떨어지는 단점이 있어 개인적으로는 Spark ML이 더 쉽게 느껴짐. 
- 만든 모델을 API의 형태로 제공할 수 있게 되어있는지 궁금