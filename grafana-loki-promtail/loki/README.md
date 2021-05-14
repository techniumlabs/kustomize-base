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
     eks.amazonaws.com/role-arn: arn:aws:iam::4234234324:role/Loki
   ```
3. In secret file copy encrypted code and decrpt it.
4. And Change storage the configs in that decrypted yaml file.

   ```yml

   schema_config:
   configs:
   - from: 2020-05-15
       store: aws
       object_store: s3
       schema: v11
       index:
       prefix: loki
   server:
   http_listen_port: 3100
   storage_config:
   aws:
       s3: "s3://us-east-1/loki-techniumlabs"
       dynamodb:
       dynamodb_url: "dynamodb://us-east-1"

   ```

5. Again Encrypt that yaml file and save it in secret file and install kustomization.
