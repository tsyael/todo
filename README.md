# Steps for helm chart creation - Advanced DevOps course

(1) Create the new helm chart : 

`helm create exe3`

(2) Changes performed on values.yaml : 

`replicaCount: 3`

`repository: avielb/todo`

(3) Verify the created chart :

`helm template ./ --debug`

(4) Installing the new chart : 

`helm upgrade -i exe3 ./`

(5) Changes performed on templates/deployment.yaml (due to image pulling issue - see below) : 

`image: "{{ .Values.image.repository }}"`

* Relevant error :  Failed to pull image "avielb/todo:1.16.0": rpc error: code = Unknown desc = Error response from daemon: toomanyrequests: You have reached your pull rate limit. You may increase the limit by authenticating and upgrading: https://www.docker.com/increase-rate-limit

(6) Delete the current chart + recreate it :

`helm delete exe3`

`helm upgrade -i exe3 ./`

(7) Verify the created 3 pods + service : 

`kubectl get pods`

![image](https://user-images.githubusercontent.com/37069188/173135256-c1958bd8-51fd-42d5-98ef-3dc33d9cf0d4.png)

`kubectl get services`

![image](https://user-images.githubusercontent.com/37069188/173135368-0c33a8ee-f607-4113-a9b2-f06096b8b8e4.png)

(8) Verify that the deployed application is accessible : 

* Use the following commands to retrieve the URL to be used : 

`export NODE_PORT=$(kubectl get --namespace default -o jsonpath="{.spec.ports[0].nodePort}" services exe3`

`export NODE_IP=$(kubectl get nodes --namespace default -o jsonpath="{.items[0].status.addresses[0].address}")`

`echo http://$NODE_IP:$NODE_PORT`

![image](https://user-images.githubusercontent.com/37069188/173135710-bb519e49-f632-453a-9509-79b1f9a72fc0.png)

* Access the retrieved URL using a browser : 

![todos_pic_browser](https://user-images.githubusercontent.com/37069188/173135802-e57c0151-4cdb-4e27-ad2f-6553b2fbe547.PNG)






