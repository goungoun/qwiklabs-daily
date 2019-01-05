# MapReduce in Dataflow(Python)
- Levels: advanced
- Link:https://googlecoursera.qwiklabs.com/catalog_lab/1370
- Git: https://github.com/GoogleCloudPlatform/training-data-analyst 

# Summary
- Java 파일에서 import 로 시작되는 구문만 빼서 제일 많이 사용한 패키지가 무엇인지 분석
- 맵 리듀스를 Apache beam으로 작성하여 cloud shell(local)에서 실행시키는 예
제

## Prep
- 준비작업
~~~bash
sudo apt-get install python-pip -y

pip install google-cloud-dataflow oauth2client==3.0.0
# downgrade as 1.11 breaks apitools
sudo pip install --force six==1.10
pip install -U pip
pip -V
sudo pip install apache_beam
~~~

## Source
~~~python
import apache_beam as beam
import argparse

# startsWith, packageUse 등을 여기에 정의
...

if __name__ == '__main__':
   parser = argparse.ArgumentParser(description='Find the most used Java packages')
   parser.add_argument('--output_prefix', default='/tmp/output', help='Output prefix')
   ...

   options, pipeline_args = parser.parse_known_args()
   p = beam.Pipeline(argv=pipeline_args)

   input = '{0}*.java'.format(options.input)
   output_prefix = options.output_prefix
   keyword = 'import'

   # find most used packages
   (p
      | 'GetJava' >> beam.io.ReadFromText(input)
      | 'GetImports' >> beam.FlatMap(lambda line: startsWith(line, keyword))
      | 'PackageUse' >> beam.FlatMap(lambda line: packageUse(line, keyword))
      | 'TotalUse' >> beam.CombinePerKey(sum)
      | 'Top_5' >> beam.transforms.combiners.Top.Of(5, by_value)
      | 'write' >> beam.io.WriteToText(output_prefix)
   )

   p.run().wait_until_finish()
~~~
> https://github.com/GoogleCloudPlatform/training-data-analyst/blob/master/courses/data_analysis/lab2/python/is_popular.py

## Comment
- Java code는 꼭 annotation들어간 spring같고 python코드는 bash로 처리할 때 자주 쓰는 파이프가 있어 bash같은 python 코드같이 보이는 것은 나만 그런 것인가?
- 실제 어떻게 코딩하고 디버깅하는지 라이브 코딩하는 모습을 봐야할 것 같다. Dataproc도 python shell이나 jupyter notebook을 사용한 interactive한 코딩이 가능할지?