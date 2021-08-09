# Falco intallation with EKS

## Step 1
- Create the EKS cluster
## Step 2
- Deploy your project in cluster
## Step 3
- Clone the falco repository from git
  ```bash
    git clone https://github.com/sysdiglabs/falco-aws-firelens-integration
  ```
## Step 4
- Now go to the directory, eks/fluent-bit, and you will find two directories called aws and kubernetes
- aws – This is the directory that has the IAM policy called iam_role_policy.json, which we will attach to the worker node VM’s role, which is automatically attached to the worker nodes when we create or deploy an EKS cluster. This policy will give Falco running on the worker nodes to send/stream logs to Amazon CloudWatch.
- Kubernetes – This directory has three files: configmap.yaml, daemonset.yaml, and service-account.yaml. These files will be applied to create a ConfigMap for Fluent Bit configuration, a Fluent Bit DaemonSet to run on all worker nodes, and finally a service account for the RBAC cluster role for authorization. All the files will be applied all at once.

- We will attach the IAM policy to the node instances to give them permissions to stream logs to Amazon CloudWatch as below –
    ```bash
    aws iam create-policy --policy-name EKS-CloudWatchLogs --policy-document file://./fluent-bit/aws/iam_role_policy.json
    ```
    ```bash
    aws iam attach-role-policy --role-name <EKS-NODE-ROLE-NAME> --policy-arn `aws iam list-policies | jq -r '.[][] | select(.PolicyName == "EKS-CloudWatchLogs") | .Arn'`
    ```
- Finally, apply the whole directory and all the listed configuration files (Configmap.yaml, daemonset.yaml and service-account.yaml) will be applied.
    ```bash
    kubectl apply -f eks/fluent-bit/kubernetes/
    ```
## Step 5
- Finally install the falco from kustomize
    ```bash 
    kubectl apply -k ./ 
    ```
sample values.yaml is here below for your reference.

https://github.com/falcosecurity/charts/blob/master/falco/values.yaml