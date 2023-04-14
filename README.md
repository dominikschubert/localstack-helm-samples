# localstack-helm-samples

A few sample HELM value files for different scenarios to run Lambda on LocalStack in Kubernetes.


```bash
k3d cluster create ls-test-cluster
helm repo add localstack https://localstack.github.io/helm-charts
helm repo update
helm install -f <values-file> localstack-test localstack/localstack

# currently needs to restart the localstack process so that the docker client works properly
# can be removed after adding a dependency between the LS and dind container so that LS waits
#   until dind is operable 

helm exec -it <pod> -- /bin/bash

# in the container
kill -10 1
```

Wait until LS is fully up and then deploy & invoke a lambda function:

```bash
aws lambda create-function \
            --endpoint-url="http://192.168.208.2:4566" \
            --function-name my-function \
            --runtime python3.9 \
            --zip-file fileb://app.zip \
            --handler app.handler \
            --role arn:aws:iam::000000000000:role/something
aws --endpoint-url "http://192.168.208.2:4566" lambda wait function-active-v2 --function-name my-function
aws --endpoint-url "http://192.168.208.2:4566" lambda invoke --function-name my-function out
```
