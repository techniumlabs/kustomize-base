# Installation

    ```sh

    $ kubectl create namespace cert-manager

    /cert-manager$ kubectl apply -k ./

    ```

# DNS certification setup

1. Create AWS IAM policy
   cert-manager needs to be able to add records to Route53 in order to solve the DNS01 challenge. To enable this, create a IAM policy with the following permissions:

   ```json
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Action": "route53:GetChange",
         "Resource": "arn:aws:route53:::change/*"
       },
       {
         "Effect": "Allow",
         "Action": [
           "route53:ChangeResourceRecordSets",
           "route53:ListResourceRecordSets"
         ],
         "Resource": "arn:aws:route53:::hostedzone/*"
       },
       {
         "Effect": "Allow",
         "Action": "route53:ListHostedZonesByName",
         "Resource": "*"
       }
     ]
   }
   ```

2. Create web identity role with cert-manager policy and update trust relationship for cert-maneger service account name

   ```json
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Principal": {
           "Federated": "arn:aws:iam::54545454:oidc-provider/oidc.eks.us-east-1.amazonaws.com/id/123123123SDFS334R34FDS"
         },
         "Action": "sts:AssumeRoleWithWebIdentity",
         "Condition": {
           "StringEquals": {
             "oidc.eks.us-east-1.amazonaws.com/id/123123123SDFS334R34FDS:sub": "system:serviceaccount:<namespace>:<cert-service-account-name>"
           }
         }
       }
     ]
   }
   ```

3. Update service account annotations with cert-manager role arn

   ```yaml
   apiVersion: v1
   kind: ServiceAccount
   automountServiceAccountToken: true
   metadata:
   annotations:
     eks.amazonaws.com/role-arn: arn:aws:iam::21321324:role/cert-manager
   name: cert-manager
   ```

4. Create ClusterIssuer

   ```yaml
   apiVersion: cert-manager.io/v1alpha2
   kind: ClusterIssuer
   metadata:
   name: letsencrypt
   spec:
   acme:
     # You must replace this email address with your own.
     # Let's Encrypt will use this to contact you about expiring
     # certificates, and issues related to your account.
     email: name@email.com
     server: https://acme-v02.api.letsencrypt.org/directory
     privateKeySecretRef:
     # Secret resource used to store the account's private key.
     name: your-own-very-secretive-key
     solvers:
       - dns01:
           route53:
           region: us-east-1
   ```

5. Create certificate

   ```yaml
   apiVersion: cert-manager.io/v1alpha2
   kind: Certificate
   metadata:
   name: secure-cert
   spec:
   # commonName: techniumlabs.io
   secretName: secure-cert
   dnsNames:
     - games.sbox.example.io
   issuerRef:
     name: letsencrypt
     kind: ClusterIssuer
   ```

6. Config certification with ingressrouter

   ```yaml

   apiVersion: traefik.containo.us/v1alpha1
   kind: IngressRoute
   metadata:
   name: name-ingressroute
   spec:
   entryPoints:
       - websecure
   routes:
   - kind: Rule
       match: Host(`nginxs.sbox.example.io`)
       services:
       - kind: Service
       name: service-nginx
       namespace: nginx
       passHostHeader: true
       port: 80
   tls:
       secretName: secure-cert

   ```
