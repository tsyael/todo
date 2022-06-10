# Steps for helm chart creation & uploading to ChartMuseum - Advanced DevOps course

## Helm chart creation

(1) Create the new helm chart : 

`helm create todo`

(2) Changes performed on values.yaml : 

* Configure # of replicas - 
`replicaCount: 3`

* Configure repo for pods deployment : 
`repository: avielb/todo`

* Configure NodePort service : 

  `type: NodePort`

  `port: 8080`

  `targetPort: 8080`

(3) Verify the created chart :

`helm template ./ --debug`

(4) Installing the new chart : 

`helm upgrade -i todo ./`

(5) Changes performed on templates/deployment.yaml (due to image pulling issue - see below) : 

`image: "{{ .Values.image.repository }}"`

* Relevant error :  Failed to pull image "avielb/todo:1.16.0": rpc error: code = Unknown desc = Error response from daemon: toomanyrequests: You have reached your pull rate limit. You may increase the limit by authenticating and upgrading: https://www.docker.com/increase-rate-limit

(6) Delete the current chart + recreate it :

`helm delete todo`

`helm upgrade -i todo ./`

(7) Verify the created 3 pods + service : 

`kubectl get pods`

![image](https://user-images.githubusercontent.com/37069188/173137656-f0c78b4d-e46d-4493-8516-14ddceb34dec.png)

`kubectl get services`

![image](https://user-images.githubusercontent.com/37069188/173137694-d5c998b2-6ba5-46b9-8911-7c1863ff1061.png)

(8) Verify that the deployed application is accessible : 

* Use the following commands to retrieve the URL to be used : 

`export NODE_PORT=$(kubectl get --namespace default -o jsonpath="{.spec.ports[0].nodePort}" services exe3`

`export NODE_IP=$(kubectl get nodes --namespace default -o jsonpath="{.items[0].status.addresses[0].address}")`

`echo http://$NODE_IP:$NODE_PORT`

![image](https://user-images.githubusercontent.com/37069188/173137511-33efbb06-7e50-4e13-92e4-7e8e48fec991.png)

* Access the retrieved URL using a browser : 

![image](https://user-images.githubusercontent.com/37069188/173137576-da5fc179-985f-4b2a-a891-bd06a6e40be9.png)


## Uploading to ChartMuseum

Verify the chart before uploaing : 

`helm lint`

Package the chart : 

`helm package .`

Upload the chart to chartmuseum : 

`curl -L --data-binary "@todo-0.1.0.tgz" http://localhost:8081/api/charts`

Update the helm repo : 

`helm repo update`

Install from ChartMuseum : 

`helm install todo-demo chartmuseum/todo`

![image](https://user-images.githubusercontent.com/37069188/173144894-0bd1fe50-7f42-4323-8076-fe861985da05.png)




