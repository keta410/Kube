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
## **Step** #Installation [Kubernetes] on Windows and Create [Minikube]
_________________________________________________________________________

1. Install and Check verion **kubectl** on Windows 
    
    Run Powershell ด้วยสถานะ Admin

    ```shell
    $ curl.exe -LO "https://dl.k8s.io/release/v1.26.0/bin/windows/amd64/kubectl.exe"

    $ kubectl version --client
    ```
2. Install **minikube** on Windows by **Hyperkit**

    ```shell
    $ New-Item -Path 'c:\' -Name 'minikube' -ItemType Directory -Force
    Invoke-WebRequest -OutFile 'c:\minikube\minikube.exe' -Uri 'https://github.com/kubernetes/minikube/releases/latest/download/minikube-windows-amd64.exe' -UseBasicParsing
    
    #After that, config path of minikube on Environment Variables (กำหนด path ของ minikub บน Environment Variables)

    $ minikube start --driver=hyperkit
    #Start minikube by hyperkit (เริ่มต้น minikube ด้วย hyperkit)
    ```
3. Create **minikube**
    ```shell
    $ minikube dashboard
    ```
_________________________________________________________________________
## **Step** #Traefik Deploy on [MiniKube]
_________________________________________________________________________

_________________________________________________________________________
## **Step** #Web Deploy used Image [rancher/hello-world]
_________________________________________________________________________

