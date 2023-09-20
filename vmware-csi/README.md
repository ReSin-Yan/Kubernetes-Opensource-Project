# VMware vsphere csi install

vSphere Container Storage Plug-in也稱為上層 vSphere CSI 驅動是一個磁碟區插件  
在部署在 vSphere 中的 Kubernetes 叢集中運行  
負責在 vSphere 上調配持久磁碟區  
依照比較好理解的方式就是，使用VMware vsphere部屬出來的虛擬機(通常2~N台)  
為基礎，建立出來的kubernetes叢集  

簡單來說，kubernetes叢集安裝在vmware 虛擬機上面就可以使用  


 | 安裝角色 |
|-------|
| vmtools |
| Grafana    |  
| alertmanager    |


## 事先準備  

 | 事先需求 |
|-------|
| kubernetes 叢集(版本1.28.2) |
| StorageClass    |  
| ingress    |


## 安裝步驟  

### vmtools 安裝  

可以使用一般的安裝方式(掛載ISO檔案，再開啟)  
也可以通過網路下載opentools  

```
sudo apt-get install open-vm-tools
```

### 虛擬機參數測定   

###  vSphere Cloud Provider  

