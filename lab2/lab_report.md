University: [SPBU](https://spbu.ru/)
Faculty: [MM](https://math.spbu.ru/rus/)
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)
Year: 2022/2023
Group: 20.b13-mm
Author: Lurie Lilia Mikhailovna
Lab: Lab2
Date of create: 10.12.2022
Date of finished: -


## Цель работы

Ознакомиться с типами "контроллеров" развертывания контейнеров, ознакомится с сетевыми сервисами и развернуть свое веб приложение.

## Ход работы

Мной были проделаны следующие действия:
1. Создан deployment с двумя репликами контейнера [ifilyaninitmo/itdt-contained-frontend:master](https://hub.docker.com/repository/docker/ifilyaninitmo/itdt-contained-frontend).
В эти реплики были переданы переменные `REACT_APP_USERNAME` и `REACT_APP_COMPANY_NAME`.
```
apiVersion: apps/v1
kind: Deployment
metadata:
	name: pain
spec:
	selector:
		matchLabels:
			app: pain
	replicas: 2
	template:
		metadata:
			labels:
				app: pain
		spec:
			containers:
			- name: pain
				image: ifilyaninitmo/itdt-contained-frontend:master
				ports:
				- containerPort: 3000
				env:
				- name: REACT_APP_USERNAME
					value: "Lily"
				- name: REACT_APP_COMPANY_NAME
					value: "LLM"
```
2. Развёрнут minikube cluster
```
> minikube start
😄  minikube v1.28.0 on Ubuntu 22.04
✨  Using the virtualbox driver based on existing profile
👍  Starting control plane node minikube in cluster minikube
🔄  Restarting existing virtualbox VM for "minikube" ...
🐳  Preparing Kubernetes v1.25.3 on Docker 20.10.20 ...
    ▪ Using image docker.io/kubernetesui/dashboard:v2.7.0
    ▪ Using image docker.io/kubernetesui/metrics-scraper:v1.0.8
    ▪ Using image gcr.io/k8s-minikube/storage-provisioner:v5
🔎  Verifying Kubernetes components...
💡  Some dashboard features require the metrics-server addon. To enable all features please run:

	minikube addons enable metrics-server	


🌟  Enabled addons: storage-provisioner, dashboard
💡  kubectl not found. If you need it, try: 'minikube kubectl -- get pods -A'
🏄  Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default
```
```
> minikube kubectl -- apply -f pain.yaml
deployment.apps/pain created
```
3. Создан сервис, через который осуществляется доступ на это поды.
```
> minikube kubectl -- expose deploy pain --type=ClusterIP --port=3000
service/pain exposed
```
```
> minikube kubectl get all
NAME                        READY   STATUS    RESTARTS   AGE
pod/pain-6d6c9b98f7-6ggm8   1/1     Running   0          42s
pod/pain-6d6c9b98f7-6hxbp   1/1     Running   0          42s

NAME                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
service/kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP    8d
service/pain         ClusterIP   10.103.174.55   <none>        3000/TCP   15s

NAME                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/pain   2/2     2            2           42s

NAME                              DESIRED   CURRENT   READY   AGE
replicaset.apps/pain-6d6c9b98f7   2         2         2       42s
```
4. Запущен в minikube режим проброса портов и осуществлено подключение к контейнерам через веб браузер.
```
> minikube kubectl -- port-forward service/pain 3000:3000
Forwarding from 127.0.0.1:3000 -> 3000
Forwarding from [::1]:3000 -> 3000
Handling connection for 3000
```
5. Переменные `REACT_APP_USERNAME`, `REACT_APP_COMPANY_NAME` и `Container name`остаются неизменными при различных запусках.
![image](https://github.com/lilylurie/2022_2023-blockchain-20b13mm-Lurie_l_m/blob/main/lab2/Images/Screenshot%20from%202022-12-13%2017-55-40.png)
```
> minikube kubectl logs pain-6d6c9b98f7-6ggm8
Builing frontend
build finished
Server started on port 3000
```
```
> minikube kubectl logs pain-6d6c9b98f7-6hxbp
Builing frontend
build finished
Server started on port 3000
```
