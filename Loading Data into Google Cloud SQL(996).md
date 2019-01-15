# Loading Data into Google Cloud SQL
- Levels: Advanced
- Link: https://run.qwiklabs.com/catalog_lab/996

## Cheat Sheet
~~~bash
gcloud sql instances create flights \
    --tier=db-n1-standard-1 --activation-policy=ALWAYS
gcloud sql users set-password root --host % --instance flights \
 --password Passw0rd
export ADDRESS=$(wget -qO - http://ipecho.net/plain)/32
gcloud sql instances patch flights --authorized-networks $ADDRESS
MYSQLIP=$(gcloud sql instances describe \
flights --format="value(ipAddresses.ipAddress)")
mysql --host=$MYSQLIP --user=root \
      --password --verbose < create_table.sql
~~~

## Comment
- 한번 더 시도해보고 보완 필요
