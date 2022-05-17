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
### Cluster migration  
### Enable API group versions  
### Resource filtering  
### Backup reference  
### Backup hooks  
### Restore reference  
### Restore hooks  
### Run in any namespace  
### CSI Support (beta)  
### Verifying Self-signed Certificates  
### Changing RBAC permissions  

## 其他注意事項  
