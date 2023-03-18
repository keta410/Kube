# Kube

 หัวข้อ:
  - สร้าง miniKube
  - Traefik deploy บน miniKube
    (จำลองโดเมน hostfile เครื่อง client ภายใต้โมเดล spcn22.local)
  - web deploy ใช้ image "rancher/hello-world"
    (sevice ใช้บริการ reProxy traefik, ตั้งชื่อ web.spcn22.local)

 เขียนอธิบาย Read.me
 - บันทึกขั้นตอนการเตรียม miniKube ถึง web deploy 
 - yaml file ต่างๆ ที่ใช้งาน

 wakatime 3 hr

 ส่ง AssignMent: Github url, short screen [web "Show request details, traefik (หลังจาก web deploy)"], pdf file wakatime

**Wakatime of Kube Project** : 

**Ref** :
> https://github.com/iamapinan/kubeplay-traefik 

_________________________________________________________________________
## **Step** #Installation [ Kubernetes ] on Windows and Create  [ Minikube ]
_________________________________________________________________________

1. Install and Check verion **kubectl** on Windows 
    
    Run Powershell ด้วยสถานะ Admin

    ```shell
    > curl.exe -LO "https://dl.k8s.io/release/v1.26.0/bin/windows/amd64/kubectl.exe"

    > kubectl version --client
    ```
2. Install **minikube** on Windows by **Hyperkit**

    ```shell
    > New-Item -Path 'c:\' -Name 'minikube' -ItemType Directory -Force
    Invoke-WebRequest -OutFile 'c:\minikube\minikube.exe' -Uri 'https://github.com/kubernetes/minikube/releases/latest/download/minikube-windows-amd64.exe' -UseBasicParsing
    
    #After that, config path of minikube on Environment Variables (กำหนด path ของ minikub บน Environment Variables)

3. Create **minikube** and allow aceess to **Kubernetes App**  
    ```shell
    > minikube start
    > minikube dashboard

    > minikube tunnel
    ```

_________________________________________________________________________
## **Step** #Traefik Deploy on [ MiniKube ]
_________________________________________________________________________

1. Install traefik, ให้ศึกษาการติดตั้ง Traefik แบบ Step by step follows this=> [kuberplay-tragik](https://github.com/iamapinan/kubeplay-traefik)

<ins>For Download helm</ins> :
> https://github.com/helm/helm/releases/tag/v3.11.2

โดยสำหรับ helm ให้ run ผ่าน powershell on Windows จากนั้นทำการแก้ไข ```file.ymal``` ทุกไฟล์ที่มีระบุ **namespace** กำหนดให้เป็น default
```shell
##Set namespace
namespace: default
``` 
จากนั้นมีการกำหนด path ของ helm บน Environment Variables 

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

ใช้คำสั่งต่อไปนี้ผ่าน wsl(Ubuntu) on Windows เพื่อสร้าง file ของ ```auth-secret``` และ ```dashboard-secret.yml``` 
```linux
 > htpasswd -nB user | tee auth-secret    #Set password(กำหนด password)

 > kubectl create secret generic -n traefik dashboard-auth-secret --from-file=users=auth-secret -o yaml --dry-run=client | tee dashboard-secret.yaml
 #Create `dashboard-secret.yml`
```

* <ins>OUTPUT</ins> : After finsih Created ```auth-secret```
  ![htpasswd-setPSW](https://user-images.githubusercontent.com/104758471/225998200-9906106f-286c-4b9d-b4cc-6927f2afde93.jpg)

* <ins>OUTPUT</ins> : After finsih Created ```dashboard-secret.yml```
  ![minikube-dashboard-dockerAuto](https://user-images.githubusercontent.com/104758471/226010402-f2c4a94b-f277-47f5-b035-1799fe0f8e09.png)

#กรณีที่เจอ ```Command : 'htpasswd' not found```

ต้องทำการติดตั้ง *apache2-utils*
* ติดตั้ง apache2-utils => ```sudo apt install apache2-utils```
![image](https://user-images.githubusercontent.com/104758471/226003594-a9ecf798-4b7f-44d4-bd00-e4caf367d2b9.png)
จากภาพ กรอก Y ในการทำงานต่อแล้ว Run คำสั่งตามลำดับต่อไป

* <ins>OUTPUT</ins> : After finsih Created  ```auth-secret``` and ```dashboard-secret.yml```, it's show

![Add2secreat](https://user-images.githubusercontent.com/104758471/226095700-d6340859-3e51-4b02-a06a-4d2b33ab280b.jpg)

จากนั้นทำการ copy ที่ *user* ของ file ```dashboard-secret.yaml``` ไปเปลี่ยนที่ *user* ของ file ```traefik-dashboard.yaml```

* file ```dashboard-secret.yaml``` (copy user)
![cop-UserDashsecreat2](https://user-images.githubusercontent.com/104758471/226077479-bad79e84-2ad1-4f9e-baa1-ff1313e7dceb.jpg)

* file ```traefik-dashboard.yaml``` (paste user)
![pastUserToTraefik-dash2](https://user-images.githubusercontent.com/104758471/226078199-f1bce821-bcf8-405a-8b3b-baf8f74bc757.jpg)

ใช้สั่งต่อไปนี้เพื่อ Deploy traefik 
```shell
> kubectl apply -f traefik-dashboard.yaml
```
* <ins>SHOW</ins> : After finsih Traefik Deploy on Minikube
![out-traefik-spcn22local](https://user-images.githubusercontent.com/104758471/226078492-a267ec89-120d-439b-abb8-93d55224c6b1.jpg)

_________________________________________________________________________
## **Step** #Web Deploy used Image [ rancher/hello-world ]
_________________________________________________________________________

1. สร้างไฟล์ ```rancheer.yaml```
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

3. Deploy rancher
```
kubectl apply -f rancheer.yaml
```