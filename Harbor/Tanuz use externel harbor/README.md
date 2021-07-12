# Tanzu Guest Cluster使用外部式的Harbor  
Latest update: 2021/07/12  
Tanzu 目前分為TKGs以及TKGm兩種模式  
其中TKGs又分為使用vDS以及NSX-T版本  
再NSX-T版本中，有包含了內嵌式的Harbor，但是再vDS版本中，就沒有包含內嵌式的Harbor  
所以本內容會著重在如何再沒有內嵌式Harbor的環境下，使用外部式的Harbor  

本文參考設定  
https://www.youtube.com/watch?v=sqC9bP8gwQ0&ab_channel=VMwareTanzu  
https://docs.vmware.com/en/VMware-vSphere/7.0/vmware-vsphere-with-tanzu/GUID-376FCCD1-7743-4202-ACCA-56F214B6892F.html  

## 設定步驟  

### 環境準備  

本內容使用的方式為再7.0.2版本新增的設定`TkgServiceConfiguration方式`  
vSphere版本 >= 7.0.2  
Harbor透過https的方式安裝完成  

### 設定步驟  

#### 取得openssl 憑證  

在任何一台linux client或是tanzu的linx client端點機器輸入  
```
openssl s_client -connect <your harbor FQDN>.com:443
```
輸入之後，點`Ctrl + c`退出  

複製下Server certificate全部的資訊  
如圖所示  
需要確保連同`-----BEGIN CERTIFICATE-----`以及`-----END CERTIFICATE-----` 都要複製

#### 將文字轉換成Base64格式  

投過以下網站進行  
Base64  https://base64.guru/

左方工具列`Encoders` > `Text to Base64` 
將剛剛複製下來的資訊貼到`Text*`欄位中  
點選`Encode Text to Base64` 
圖片

將產出的內容複製下來  
#### 編輯TkgServiceConfiguration  

在vsphere7.0.2之後，針對外部的認證，新增的設定值  
先登入Tanzu的SC namespaces內  
指令對應的參數根據環境輸入  
登入IP為server 10.74.0.1  
配置的登入user administrator@vsphere.local  
配置的namespace為 demo  

```
kubectl vsphere login --insecure-skip-tls-verify --server 10.74.0.1 --vsphere-username administrator@vsphere.local
```
輸入密碼  

切換到namespace  
```
kubectl config use-context demo
```

編輯此namespace的TkgServiceConfiguration  
```
kubectl edit tkgServiceConfiguration
```
開始編輯模式之後  
參考以下方式進行修改  
新增在設定的最下面spec  

name: yourFQDN.com  
data: 前一個步驟複製下來轉成base64的認證碼  
可以有多個認證  
```
apiVersion: run.tanzu.vmware.com/v1alpha1
kind: TkgServiceConfiguration
metadata:
  name: tkg-service-configuration
spec:
  defaultCNI: antrea
  trust:
    additionalTrustedCAs:
      - name: first-cert-name
        data: base64-encoded string of a PEM encoded public cert 1
      - name: second-cert-name
        data: base64-encoded string of a PEM encoded public cert 2
```
新增完畢之後儲存離開  

#### 新增Guest Cluster並且部屬拉harbor的yaml檔案  

根據自己環境修改文件  
`name`,`namespace`,`version`,`storageClass`  
其中`class`,`count`,`volumes`建議一樣即可  
```
apiVersion: run.tanzu.vmware.com/v1alpha1
kind: TanzuKubernetesCluster
metadata:
  name: demogc1
  namespace: demo
spec:
  distribution:
    version: v1.20
  topology:
    controlPlane:
      count: 1
      class: best-effort-small
      storageClass: wcppolicy
      volumes:
        - name: etcd
          mountPath: /var/lib/etcd
          capacity:
            storage: 20Gi
    workers:
      count: 2
      class: best-effort-small
      storageClass: wcppolicy
      volumes:
        - name: containerd
          mountPath: /var/lib/containerd
          capacity:
            storage: 50Gi
```
等待建立完成之後  
切換環境過去  

```
kubectl vsphere login --insecure-skip-tls-verify --server 10.74.0.1 --vsphere-username administrator@vsphere.local --tanzu-kubernetes-cluster-name demogc1 
kubectl config use-context demogc1
```

建立Deployment服務  
其中image需要指定到harbor上的images(由前一個session產生，也可以放入自己的內容)
```
apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
kind: Deployment
metadata:
  name: nginx-ui
spec:
  selector:
    matchLabels:
      app: nginx-ui
  replicas: 2 # tells deployment to run 2 pods matching the template
  imagePullSecrets:
  template:
    metadata:
      labels:
        app: nginx-ui
    spec:
      containers:
      - name: nginx-ui
        image: resinharbor.com/demo/ubuntu:latest
        ports:
        - containerPort: 80
```

