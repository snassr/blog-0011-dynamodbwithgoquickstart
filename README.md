# DynamoDB with Go (Golang) - Quickstart
Up & Running on AWS's DynamoDB: Setup, Core Concepts & Go SDK.

## Setup
Start dynamodb docker container
```bash
docker run -p 8000:8000 --name dynodb-local amazon/dynamodb-local -jar DynamoDBLocal.jar -sharedDb
```
## Run
```
go test -v
```

## CLI
```bash
# create table
aws dynamodb create-table \
  --table-name Movies \
  --attribute-definitions \
      AttributeName=year,AttributeType=N \
      AttributeName=title,AttributeType=S \
  --key-schema \
      AttributeName=year,KeyType=HASH \
      AttributeName=title,KeyType=RANGE \
  --billing-mode PROVISIONED \
  --provisioned-throughput \
      ReadCapacityUnits=10,WriteCapacityUnits=10 \
  --endpoint-url=http://localhost:8000
# put item (note: cleared if executed before repo's go program)
aws dynamodb put-item \
  --table-name Movies \
  --item \
'{"year": {"N": "1944"}, "title": {"S": "Captain America"}, "hasFavreau": {"BOOL": false}, "phase": {"S": "0"}}' \
  --endpoint-url=http://localhost:8000
# get item
aws dynamodb get-item \
  --table-name Movies \
  --key '{"year": {"N": "1944"}, "title": {"S": "Captain America"}}' \
  --endpoint-url=http://localhost:8000
# range query
aws dynamodb query \
  --table-name Movies \
  --key-condition-expression "#y = :yr" \
  --projection-expression "title,hasFavreau" \
  --expression-attribute-names '{"#y":"year"}' \
  --expression-attribute-values '{":yr":{"N":"1944"}}' \
  --endpoint-url=http://localhost:8000
# scan query
aws dynamodb scan \
  --table-name Movies \
  --filter-expression "hasFavreau = :hasFav" \
  --expression-attribute-values '{":hasFav": {"BOOL": false}}' \
  --endpoint-url=http://localhost:8000
```
