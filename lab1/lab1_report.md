University: [SPBU](https://spbu.ru/)
Faculty: [MM](https://math.spbu.ru/rus/)
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)
Year: 2022/2023
Group: 20.b13-mm
Author: Lurie Lilia Mikhailovna
Lab: Lab1
Date of create: 4.12.2022
Date of finished: -


## Цель работы

Ознакомиться с инструментами Minikube и Docker, развернуть свой первый "под".

## Ход работы

Мной были проделаны следующие действия:
1. Изучена [документация по Minikube](https://minikube.sigs.k8s.io/docs/)
2. Установлен Docker
  ```
 > sudo systemctl status docker
● docker.service - Docker Application Container Engine
     Loaded: loaded (/lib/systemd/system/docker.service; enabled; vendor preset: enabled)
     Active: active (running) since Sun 2022-12-04 21:27:54 MSK; 2min 39s ago
TriggeredBy: ● docker.socket
       Docs: https://docs.docker.com
   Main PID: 474475 (dockerd)
      Tasks: 18
     Memory: 24.7M
        CPU: 254ms
     CGroup: /system.slice/docker.service
             └─474475 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock
```
3. Установлен Minikube
```
> minikube status
minikube
type: Control Plane
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured
```
4. Развёрнут minikube cluster
```
> minikube start
😄  minikube v1.28.0 on Ubuntu 22.04
✨  Automatically selected the virtualbox driver. Other choices: none, ssh
💿  Downloading VM boot image ...
    > minikube-v1.28.0-amd64.iso....:  65 B / 65 B [---------] 100.00% ? p/s 0s
    > minikube-v1.28.0-amd64.iso:  274.45 MiB / 274.45 MiB  100.00% 3.36 MiB p/
👍  Starting control plane node minikube in cluster minikube
💾  Downloading Kubernetes v1.25.3 preload ...
    > preloaded-images-k8s-v18-v1...:  385.44 MiB / 385.44 MiB  100.00% 3.45 Mi
🔥  Creating virtualbox VM (CPUs=2, Memory=2200MB, Disk=20000MB) ...
🐳  Preparing Kubernetes v1.25.3 on Docker 20.10.20 ...
    ▪ Generating certificates and keys ...
    ▪ Booting up control plane ...
    ▪ Configuring RBAC rules ...
    ▪ Using image gcr.io/k8s-minikube/storage-provisioner:v5
🔎  Verifying Kubernetes components...
🌟  Enabled addons: storage-provisioner, default-storageclass
🏄  Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default
```
5. Написан манифест для развёртывания пода Hashicorp Vault
```
apiVersion: apps/v1
kind: Deployment
metadata:
	name: vault
spec:
	selector:
		matchLabels:
			app: vault
	template:
		metadata:
			labels:
				app: vault
		spec:
			containers:
			  - name: vault
				image: vault
				resources:
					limits:
						memory: "128Mi"
						cpu: "500m"
				ports:
				  - containerPort: 8200
```

```
> kubectl get all
NAME                         READY   STATUS    RESTARTS   AGE
pod/vault-754d776958-d9nqx   1/1     Running   0          52s

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   5d21h

NAME                    READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/vault   1/1     1            1           52s

NAME                               DESIRED   CURRENT   READY   AGE
replicaset.apps/vault-754d776958   1         1         1       52s
```
6. Создан сервис ля доступа к контейнеру
```
> minikube kubectl -- expose pod vault-754d776958-d9nqx --type=NodePort --port=8200
service/vault-754d776958-d9nqx exposed
```
7. Далее, чтобы попасть в контейнер, я воспользовалась командой
```
> minikube kubectl -- port-forward service/vault-754d776958-d9nqx 8200:8200
Forwarding from 127.0.0.1:8200 -> 8200
Forwarding from [::1]:8200 -> 8200
```
8. Теперь в vault можно было зайти по ссылке [http://localhost:8200](http://localhost:8200)
![image](https://github.com/lilylurie/2022_2023-blockchain-20b13mm-Lurie_l_m/blob/main/lab1/Images/Screenshot%20from%202022-12-10%2019-44-59.png)
