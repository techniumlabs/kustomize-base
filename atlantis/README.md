# Installation
1. Create policy for terraform deployment

2. Create web identity role with policy and update trustrelationship with service account name

```json
     "StringEquals": {
          "oidc.eks.us-east-1.amazonaws.com/id/2434234324324234324324324:sub": "system:serviceaccount:default:atlantis-controller"
        }
```
3. Update webhook argument for repository in bundle.yaml file


4. Run Kustomization file

        /atlantis$ kubectl apply -k ./ 
