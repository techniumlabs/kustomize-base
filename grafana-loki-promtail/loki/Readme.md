# Installation

    $ kubectl create namespace loki

    /loki$ kubectl apply -k ./ -n loki

# S3 and DynamoDB config

1. Create policy with s3 and dynamodb access and create Role with that policy and update trust relationship with service account name. [more](https://grafana.com/docs/loki/latest/operations/storage/)
   ```json
   "StringEquals": {
               "oidc.eks.us-east-1.amazonaws.com/id/123123123SDFS334R34FDS:sub": "system:serviceaccount:<namespace>:<loki-service-account-name>"
               }
   ```
2. Add Role ARN in service account annotation.

   ```yaml
   annotations:
     eks.amazonaws.com/role-arn: arn:aws:iam::5345435345:role/Loki
   ```

3. Change storage the configs in that configmap.yaml file.

   ```yml
    schema_config:
      configs:
      - from: 2020-05-15
        store: aws
        object_store: s3
        schema: v11
        index:
          prefix: loki
    
    storage_config:
      aws:
        s3: "s3://us-east-1/loki-techniumlabs"
        dynamodb:
         dynamodb_url: "dynamodb://us-east-1"

   ```

4. Update datasource url in grafana with gateway url.
[flow](https://github.com/techniumlabs/kustomize-base/blob/master/grafana-loki-promtail/loki/file/flow%20drawing.svg)
