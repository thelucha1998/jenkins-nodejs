kubectl create secret docker-registry my-registry-secret \
  --docker-server=<REGISTRY_URL> \
  --docker-username=<USERNAME> \
  --docker-password=<PASSWORD> \
  --docker-email=<EMAIL>


Ex:
kubectl create secret docker-registry regcred \
  --docker-server=https://index.docker.io/v1/ \
  --docker-username=myusername \
  --docker-password=mypassword \
  --docker-email=myemail@example.com

deployment.yaml:
    spec:
      containers:
        - name: my-app
          image: my-private-registry.com/my-app:v1
      imagePullSecrets:
        - name: regcred
