# Installation

    $ kubectl create namespace grafana

    /grafana$ kubectl apply -k ./ -n grafana

# Postgres config

1. in deployment.yaml

    ```yaml
    
     spec:
      containers:
        - name: grafana
          image: grafana/grafana
          env: 
          - name: "GF_DATABASE_URL"
            value: "postgres://username:passwor@hostname:5432/database"
          ports:
            - name: http
              containerPort: 3000

    ```