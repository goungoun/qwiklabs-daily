# Side Inputs (python)

## Summary
- 21만 9천여개의 소스코드가 `fn_bigquery.github_extracts.contents_java_2016`에 들어있음
- 이 소스를 가지고 Popularity는 다른 소스코드에서 참조한 수를, Need Help는 TODO나 HELP annotation이 들어있는 것을 카운트
- workflow 구성
~~~
Sub1: ReadFromBQ, PackageHelp, TotalHelp, DropZero
Sub2: ReadFromBQ, PackageUse, TotalUse, Top_NNN
Sub1 + Sub2 , Scores, WriteToStorage
~~~
- keyword: `dataflow` `DirectRunner` `DataflowRunner`

## CheatSheet
- `--DirectRunner`를 사용해서 local에서 먼저 실행시켜보고 `--DataFlowRunner`를 사용해서 Dataflow로 실행시켜본다.
~~~bash
python JavaProjectsThatNeedHelp.py --bucket $BUCKET --project $DEVSHELL_PROJECT_ID --DirectRunner
python JavaProjectsThatNeedHelp.py --bucket $BUCKET --project $DEVSHELL_PROJECT_ID --DataFlowRunner
~~~

## Source Code
- Popularity와 Need Help를 구하는 예제
~~~python
def is_popular(pcoll):
 return (pcoll
    | 'PackageUse' >> beam.FlatMap(lambda rowdict: packageUse(rowdict['content'], 'import'))
    | 'TotalUse' >> beam.CombinePerKey(sum)
    | 'Top_NNN' >> beam.transforms.combiners.Top.Of(TOPN, by_value) )
~~~
- Need Help는 FIXME나 TODO가 소스에서 발견되면 카운트
~~~python
def needs_help(pcoll):
 return (pcoll
    | 'PackageHelp' >> beam.FlatMap(lambda rowdict: packageHelp(rowdict['content'], 'package'))
    | 'TotalHelp' >> beam.CombinePerKey(sum)
    | 'DropZero' >> beam.Filter(lambda packages: packages[1]>0 ) )
~~~

## Comment
- 작은 dataset을 다루는 job인 경우 directRunner를 사용해서 local server에서 실행시키는 것이 job이 훨씬 빨리 끝난다
- DAG를 사용하기 때문인지 몰라도 실행이 시작 node에서 부터 끝 node로 흘러가지는 않고 모니터링해보면 WriteToStorage쪽이 먼저 끝나는 것처럼 보이는 현상 (Support에 문의 필요)
Looks like bottom to top. First WriteToStorage is done, and then I found Top_NNN part is succeeded

side input이 뭔가? map join과 비슷한 것이라 함 그러면 어느 부분이 전체 노드로 들어가는 것인지?
파이프라인이 분기되는 부분과 sequential 하게 작성되는 부분의 문법이 어떻게 되어있는가?