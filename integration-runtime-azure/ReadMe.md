## Setup
1. Download the source code from [here](https://github.com/Azure/Azure-Data-Factory-Integration-Runtime-in-Windows-Container)

2. Download latest integration runtime file and place into SHIR folder

3. Build docker image and push into ACR

4. Update Data Factory key in deployment env 

    ```yaml
        containers:
        - name: integration-runtime
            image: <acr-name>.azurecr.io/<image-name>
            env:
            - name: ENABLE_HA
            value: "true"
            - name: HA_PORT
            value: "80"
            - name: AUTH_KEY
            value: "<data-factory-key>"
            - name: NODE_NAME
            value: "<node_name>"
    ```

5. Deploy docker image into cluster
```sh
    integration-runtime-azure$ kubectl apply -k .
```