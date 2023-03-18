# Kube

**Wakatime of Kube Project** : 
* > https://wakatime.com/@spcn22/projects/ikhglqlxzg 

**Ref** :

<ins>Traefik</ins>
* > https://github.com/iamapinan/kubeplay-traefik 

<ins>Kubectl</ins>
* > https://kubernetes.io/docs/tasks/tools/install-kubectl-windows/

<ins>Minikube</ins>
* > https://minikube.sigs.k8s.io/docs/start/ 
_________________________________________________________________

## **Contents Kube (สารบัญเนื้อหา Kube)**
-----------------
No. |English Title (ชื่อเรื่องภาษาอังกฤษ)  | Thai Title (ชื่อเรื่องภาษาไทย) |
----- |----- | ----- |
1)|[Step #Installation [ Kubernetes ] on Windows and Create  [ Minikube ]](https://github.com/keta410/Kube#step-installation--kubernetes--on-windows-and-create---minikube-)|ขั้นตอน การติดตั้ง [Kubernetes] บน Windows and Create [ Minikube ]|
2)|[Step #Traefik Deploy on [ MiniKube ]](https://github.com/keta410/Kube#step-traefik-deploy-on--minikube-)|ขั้นตอน Traefik Deploy บน [ MiniKube ]|
3)|[Step #Web Deploy used Image [ rancher/hello-world ]](https://github.com/keta410/Kube#step-web-deploy-used-image--rancherhello-world-)|ขั้นตอน Web Deploy ใช้ Image [ rancher/hello-world ]|
4)|[Set file hosts on Windows]()|ตั้งค่า file hosts บน Windows|
5)|[Expected Results]()|ผลที่คาดว่าจะได้รับ|
_________________________________________________________________________

## **Step** #Installation [ Kubernetes ] on Windows and Create [ Minikube ]
(ขั้นตอน การติดตั้ง [Kubernetes] บน Windows and Create [ Minikube ])
_________________________________________________________________________

1. **Install and check verion *kubectl* on Windows.** (ติดตั้งและเช็คเวอร์ชั่น kubectl บน Windows)

    **Run Powershell in Admin state.** ( Run Powershell ด้วยสถานะ Admin )

    ```shell
    > curl.exe -LO "https://dl.k8s.io/release/v1.26.0/bin/windows/amd64/kubectl.exe"

    > kubectl version --client
    ```
2. **Install *minikube* on Windows.** (ติดตั้ง minikube บน Windows)

    ```shell
    > New-Item -Path 'c:\' -Name 'minikube' -ItemType Directory -Force
    Invoke-WebRequest -OutFile 'c:\minikube\minikube.exe' -Uri 'https://github.com/kubernetes/minikube/releases/latest/download/minikube-windows-amd64.exe' -UseBasicParsing
    
    #After that, config path of minikube on Environment Variables. (กำหนด path ของ minikub บน Environment Variables)

3. **Create *minikube* and allow aceess to *Kubernetes App*.** (สร้าง minikube และอนุญาตการเข้าถึง Kubernetes App)  
    ```shell
    > minikube start
    #Start using minitube(เริ่มต้นการใช้งาน minikube)
    > minikube dashboard
    #Commands for minitube dashboards(คำสั่งแสดง dashboard ของ minikube)
    > minikube tunnel
    #Config, Available Kubernetes(กำหนดให้สามารถใช้ Kubernetes ได้)
    ```
**To prepare the device, install using Kubernetes, install [*kubectl*](https://kubernetes.io/docs/tasks/tools/install-kubectl-windows/) and [*minikube*](https://minikube.sigs.k8s.io/docs/start/) as appropriate.**

(การเตรียมความพร้อมของอุปกรณ์, การติดตั้งใช้ Kubernetes ให้ติดตั้ง [*kubectl*](https://kubernetes.io/docs/tasks/tools/install-kubectl-windows/) และ [*minikube*](https://minikube.sigs.k8s.io/docs/start/) ตามความเหมาะสม)
_________________________________________________________________________
## **Step** #Traefik Deploy on [ MiniKube ]
(ขั้นตอน Traefik Deploy บน [ MiniKube ])
_________________________________________________________________________

1. **Learn How to install Traefik step by step.** (ศึกษาการติดตั้ง Traefik ตามขั้นตอน) => [kuberplay-tragik](https://github.com/iamapinan/kubeplay-traefik)

**<ins>Download file helm</ins>** => [helm-v3.11.2](https://github.com/helm/helm/releases/tag/v3.11.2)

**For config, Run PowerShell on Windows, and then edit ```file. ymal``` All files specified as *namespaces : default***

(โดยสำหรับ helm ให้ run ผ่าน powershell on Windows จากนั้นทำการแก้ไข ```file.ymal``` ทุกไฟล์ที่มีระบุ *namespace : default*)
```shell
##Set namespace
namespace: default
``` 
**Then define the path of helm on the environment variable.**

(จากนั้นมีการกำหนด path ของ helm บน Environment Variables)

**In the file ```traefik-dashboard.yaml```, define the host name as ```traefik.spcn22.local``` in the matching routing specification.**

(ใน file ```traefik-dashboard.yaml``` กำหนดในส่วน spec ที่ routes ที่ match ชื่อ Host คือ ```traefik.spcn22.local``` )

<details><summary>CLICK show code : <ins>traefik-dashboard.yaml</ins></summary>
<p>

```ruby
apiVersion: traefik.containo.us/v1alpha1
kind: Middleware
metadata:
  name: traefik-basic-authen
  namespace: default
spec:
  basicAuth:
    secret: dashboard-auth-secret
    removeHeader: true
---
apiVersion: v1
data:
  users: dXNlcjokMnkkMDUkR0Z3WUZKWkVIdUZlVEoxb3hOMnB0dXBURXpIWEN4djRZenQ4STV3T1kxcTFsZmZxY2M5T0cKCg==
kind: Secret
metadata:
  name: dashboard-auth-secret
  namespace: default
---
apiVersion: traefik.containo.us/v1alpha1
kind: IngressRoute
metadata:
  name: traefik-dashboard
  namespace: default
  annotations:
    kubernetes.io/ingress.class: traefik
    traefik.ingress.kubernetes.io/router.middlewares: traefik-basic-authen
spec:
  entryPoints:
    - websecure
  routes:
    - match: Host(`traefik.spcn22.local`) && (PathPrefix(`/dashboard`) || PathPrefix(`/api`))
      kind: Rule
      middlewares:
        - name: traefik-basic-authen
          namespace: default
      services:
        - name: api@internal
          kind: TraefikService
```
<p>
</details>

**Use the following command to create files for ```auth-secret``` and ```dashboard-secret.yml``` by wsl (Ubuntu) on Windows.**
(ใช้คำสั่งต่อไปนี้ผ่าน wsl(Ubuntu) on Windows เพื่อสร้าง file ของ ```auth-secret``` และ ```dashboard-secret.yml```) 
```linux
 > htpasswd -nB user | tee auth-secret    #Set password(กำหนด password)

 > kubectl create secret generic -n traefik dashboard-auth-secret --from-file=users=auth-secret -o yaml --dry-run=client | tee dashboard-secret.yaml
 #Create `dashboard-secret.yml`
```

* **<ins>OUTPUT</ins> : After finsih Created ```auth-secret```**
(หลังสร้าง ```auth-secret```)
  ![htpasswd-setPSW](https://user-images.githubusercontent.com/104758471/225998200-9906106f-286c-4b9d-b4cc-6927f2afde93.jpg)

* **<ins>OUTPUT</ins> : After finsih Created ```dashboard-secret.yml```**
(หลังสร้าง ```dashboard-secret.yml```)
  ![minikube-dashboard-dockerAuto](https://user-images.githubusercontent.com/104758471/226010402-f2c4a94b-f277-47f5-b035-1799fe0f8e09.png)

* **<ins>OUTPUT</ins> : After finsih Created  ```auth-secret``` and ```dashboard-secret.yml```, it's show**
(หลังสร้าง ```auth-secret``` และ ```dashboard-secret.yml``` จะแสดง)
![Add2secreat](https://user-images.githubusercontent.com/104758471/226095700-d6340859-3e51-4b02-a06a-4d2b33ab280b.jpg)

**Then copy *user* of file ```dashboard-secret.yaml``` to *user* of file ```traefik-dashboard.yaml```** 

(จากนั้นทำการ copy ที่ *user* ของ file ```dashboard-secret.yaml``` ไปเปลี่ยนที่ *user* ของ file ```traefik-dashboard.yaml``` )

* **file ```dashboard-secret.yaml``` (copy user)**
![cop-UserDashsecreat2](https://user-images.githubusercontent.com/104758471/226077479-bad79e84-2ad1-4f9e-baa1-ff1313e7dceb.jpg)

* **file ```traefik-dashboard.yaml``` (paste user)**
![pastUserToTraefik-dash2](https://user-images.githubusercontent.com/104758471/226078199-f1bce821-bcf8-405a-8b3b-baf8f74bc757.jpg)

**Use the following command to Traefik Deploy.**
(ใช้สั่งต่อไปนี้เพื่อ Traefik Deploy) 
```shell
> kubectl apply -f traefik-dashboard.yaml
```
_________________________________________________________________________
## **Step** #Web Deploy used Image [ rancher/hello-world ]
(ขั้นตอน Web Deploy ใช้ Image [ rancher/hello-world ])
_________________________________________________________________________

1. **Create file** ```rancheer.yaml``` (สร้างไฟล์ ```rancheer.yaml```)

**In the file ```rancher.yaml```, define the host name as ```web.spcn22.local``` in the matching routing specification.**

(ใน file ```rancher.yaml``` กำหนดในส่วน spec ที่ routes ที่ match ชื่อ Host คือ ```web.spcn22.local``` )

<details><summary>CLICK show code : <ins>rancheer.yaml</ins></summary>
<p>

```ruby
apiVersion: v1
kind: Service
metadata:
  name: rancher
  namespace: default
spec:
  selector:
    app: rancher
  ports:
  - port: 80
    targetPort: 80
---

apiVersion: apps/v1
kind: Deployment
metadata:
  name: rancher
spec:
  selector:
    matchLabels:
      app: rancher
  template:
    metadata:
      labels:
        app: rancher
    spec:
      containers:
      - name: rancher
        image: rancher/hello-world
        resources:
          limits:
            memory: "128Mi"
            cpu: "100m"
        ports:
        - containerPort: 80
---
apiVersion: traefik.containo.us/v1alpha1
kind: IngressRoute
metadata:
  name: traefik-ingress
  namespace: default 
spec:
  entryPoints:
    - web
    - websecure
  routes:
  - match: Host(`spcn22.local`)
    kind: Rule
    services:
    - name: rancher
      port: 80
```
</p>
</details>

2. **Deploy rancher**
```
kubectl apply -f rancheer.yaml
```
_________________________________________________________________________
## **Set file hosts on Windows**
(ตั้งค่า file hosts บน Windows)
_________________________________________________________________________

* **Open file hosts of Windows and Set the IP Address and hostname from the ```rancher.yaml``` and ```traefik-dashboard.yaml``` as** (เปิด file hosts ของ Windows และตั้งค่า IP Address และชื่อ hostname จาก ```rancher.yaml``` and ```traefik-dashboard.yaml``` คือ ) : traefik.spcn22.local, web.spcn22.local

<ins>Form Config in file hosts</ins>:

```
127.0.0.1       traefik.spcnxx.local
127.0.0.1       web.spcnxx.local
```

_________________________________________________________________________
## **Expected Results**
(ผลที่คาดว่าจะได้รับ)
_________________________________________________________________________

* **<ins>SHOW</ins> : After finsih Traefik Deploy on Minikube**
(หลัง Traefik Deploy บน Minikube)

![out-traefik-spcn22local](https://user-images.githubusercontent.com/104758471/226078492-a267ec89-120d-439b-abb8-93d55224c6b1.jpg)

* **<ins>SHOW</ins> : After finsih Deploy rancher**
(หลัง Deploy rancher)

![rancher-spcn22-local](https://user-images.githubusercontent.com/104758471/226115311-17475876-0406-4ced-8a65-5111e96bb2ca.jpg)

* **<ins>SHOW</ins> : After finsih All Deploy**
(หลัง Deploy ทั้งหมด)

![all-deploy-kubernetes](https://user-images.githubusercontent.com/104758471/226115548-4735770f-e1e9-46cc-a0d4-34e5958adf1d.jpg)
