# A Simple Dataflow Pipeline (Java)
- Levels: advanced
- Link: https://googlecoursera.qwiklabs.com/catalog_lab/1368
- https://github.com/GoogleCloudPlatform/training-data-analyst

## Summary
- 자바 소스에서 import 만 필터링하여 bucket에 저장하는 예제

## mvn `archetype:generate`
~~~bash
git clone https://github.com/GoogleCloudPlatform/training-data-analyst
cd ~/training-data-analyst/courses/data_analysis/lab2
mvn archetype:generate \
  -DarchetypeArtifactId=google-cloud-dataflow-java-archetypes-starter \
  -DarchetypeGroupId=com.google.cloud.dataflow \
  -DgroupId=com.example.pipelinesrus.newidea \
  -DartifactId=newidea \
  -Dversion="[1.0.0,2.0.0]" \
  -DinteractiveMode=false
~~~

## mvn compile -e exec:java
~~~
cd ~/training-data-analyst/courses/data_analysis/lab2

export PATH=/usr/lib/jvm/java-8-openjdk-amd64/bin/:$PATH
cd ~/training-data-analyst/courses/data_analysis/lab2/javahelp
mvn compile -e exec:java \
 -Dexec.mainClass=com.google.cloud.training.dataanalyst.javahelp.Grep
~~~

## to Cloud
~~~
gsutil cp ../javahelp/src/main/java/com/google/cloud/training/dataanalyst/javahelp/*.java gs://$BUCKET/javahelp
~~~