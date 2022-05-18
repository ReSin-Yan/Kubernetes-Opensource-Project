Last update: 2022/05/17  
# Velero安裝方式

Velero本身是一個套件檔案  
可以執行在Linux,MacOS,windows上  
本篇使用Linux為基礎  

Velero是由VMware提出並維護的開源套件
如果需要使用本身是不用收費  
但是如果需要到enterprise等級的support  
或是進階的錯誤排除，就會需要額外購買support  

 參考文件  
 [Velero Docs](https://velero.io/docs/v1.8/ "link")  
 [Velero install](https://hackmd.io/@pichuang-note/rJaCFCtT_ "link")  

 | 套件名稱 | 角色  |
|-------|-------|
| MiniO | S3 Storage |  
| Velero | backup api ver1.8 |  


## 事先準備  

 | 事先需求 |
|-------|
| Velero client(Linux) |
| kubernetes(native or Tanzu)|  
| MiniO |  


## 安裝步驟  
  
### 下載velero最新版本套件    
 [Velero Download](https://github.com/vmware-tanzu/velero/releases "link")  
下載對應版本  
通常是velero-vx.x.x-linux-amd64.tar.gz  

```
wget https://github.com/vmware-tanzu/velero/releases/download/v1.8.1/velero-v1.8.1-linux-amd64.tar.gz
tar zxvf velero-v1.8.1-linux-amd64.tar.gz  
cd velero-v1.8.1-linux-amd64/  
sudo cp velero /usr/local/bin/velero  
```

設定MiniO 金鑰  

```
vim credentials-minio
```

貼入以下內容  
```
[default]
aws_access_key_id = [minio id]
aws_secret_access_key = [minio key]
```

確保有登入到一組kubernetes環境中(這邊使用TKGs，如果底層環境變換請記得確認包含plugine跟provider的參數)  
安裝 Velero  
```
velero install \
--provider aws \
--plugins velero/velero-plugin-for-aws:v1.4.1 \
--bucket [s3 bucket name] \
--secret-file credentials-minio \
--use-restic \
--use-volume-snapshots=false \
--backup-location-config region=minio-region,s3ForcePathStyle="true",s3Url=http://[minioIP]:9000,publicUrl=http://[minioIP]:9000
```


## 功能測試  

### Disaster recovery  
使用下方yaml檔案建立一個帶有網頁的服務  

```
apiVersion: v1
kind: Namespace
metadata:
  name: webip
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-ui
  namespace: webip
  labels:
    app: nginx-ui
spec:
  type: LoadBalancer
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: nginx-ui
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-ui
  namespace: webip
spec:
  replicas: 20
  selector:
    matchLabels:
      app: nginx-ui
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 1
  template:
    metadata:
      labels:
        app: nginx-ui
        tier: frontend
        secgroup: web-tier
    spec:
      containers:
      - name: nginx-ui
        image: nginxdemos/hello
        imagePullPolicy: Always
        ports:
        - containerPort: 80
```

執行此yaml之後  
利用velero的選擇器，選擇只備份namespace為webip的資料  

```
velero backup create webip-backup --include-namespaces webip
```
可以分別透過指令以及在MiniO介面看到內容    
```
velero backup get
```
圖片  

### Disaster recovery  

模擬災難發生
```
kubectl delete ns webip  
```
or  
透過yaml刪除  

接著透過指令還原檔案  
```
velero restore create --from-backup webip-backup
```

可以透過指令檢查服務是否還原  
```
kubectl get all -n webip  
```

### Cluster migration  

叢集本身預設支援跨版本以及跨平台  
但是需要考量到apiVersion的因素  
例如  
ingress在某個版本是v1  
但是在舊版本是v1beta  
諸如此類，如果需要避免此問題，需要設置EnableAPIGroupVersions  

需要準備兩個叢集  
兩個叢集之間的  
BackupStorageLocations和VolumeSnapshotLocations  
需要相同  

切換到第二個叢集之後  
進行還原  
```
velero restore create --from-backup webip-backup
```

可以觀察是否還原成功  

### Enable API group versions  
### Resource filtering  
### Backup reference  
### Backup hooks  
### Restore reference  

可以設置schedule    

```
velero schedule create NAME --schedule="* * * * *" [flags]
使用以下格式
 
# ┌───────────── minute (0 - 59)
# │ ┌───────────── hour (0 - 23)
# │ │ ┌───────────── day of the month (1 - 31)
# │ │ │ ┌───────────── month (1 - 12)
# │ │ │ │ ┌───────────── day of the week (0 - 6) (Sunday to Saturday;
# │ │ │ │ │                                   7 is also Sunday on some systems)
# │ │ │ │ │
# │ │ │ │ │
# * * * * *

#每天凌晨3點進行備份
velero schedule create example-schedule --schedule="0 3 * * *"
```

### Restore hooks  
### Run in any namespace  
### CSI Support (beta)  
### Verifying Self-signed Certificates  
### Changing RBAC permissions  

## 其他注意事項  
默認備份保留期，以 TTL（生存時間）表示，為 30 天（720 小時）；您可以--ttl <DURATION>根據需要使用該標誌來更改它。  

