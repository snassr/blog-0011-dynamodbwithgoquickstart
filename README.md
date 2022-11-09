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
# put item
aws dynamodb put-item \
  --table-name Movies \
  --item \
'{"year": {"N": "1900"}, "title": {"S": "Example 1"}}' \
  --endpoint-url=http://localhost:8000
# query table
aws dynamodb query \
  --table-name Movies \
  --key-condition-expression "#y = :yr" \
  --projection-expression "title" \
  --expression-attribute-names '{"#y":"year"}' \
  --expression-attribute-values '{":yr":{"N":"1985"}}' \
  --endpoint-url=http://localhost:8000
# scan table
aws dynamodb scan \
  --table-name Movies \
  --filter-expression "title = :name" \
  --expression-attribute-values '{":name":{"S":"Back to the Future"}}' \
  --return-consumed-capacity 'TOTAL' \
  --endpoint-url=http://localhost:8000
```
