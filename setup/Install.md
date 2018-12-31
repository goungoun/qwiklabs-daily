# Install
- Link: https://google.qwiklabs.com/catalog_lab/1309
- Link:  https://www.qwiklabs.com/catalog_lab/1311

## Python
- 최신 버전으로 update
~~~
sudo apt -y update
sudo apt -y upgrade
~~~

- install
~~~
sudo apt -y install git
sudo apt -y install python-pip
sudo pip install --upgrade pip
sudo pip install tensorflow
sudo pip install google-cloud-bigquery
sudo pip install google_compute_engine
sudo pip install google-cloud-storage
~~~

## Java
- install
~~~
sudo apt-get install -yq openjdk-8-jdk
~~~
- Make Java 8 the default
~~~
sudo update-alternatives --set java /usr/lib/jvm/java-8-openjdk-amd64/jre/bin/java
~~~

## Maven
- install
~~~
sudo apt-get install -yq maven
~~~