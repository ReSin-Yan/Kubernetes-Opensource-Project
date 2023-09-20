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
所有節點都需要安裝  

```
sudo apt-get install open-vm-tools
```

### 虛擬機參數測定   

由於vmware-csi會通過VMDK掛載的方式掛接近去虛擬機  
所以會需要有能夠修改虛擬機的權限  
所有節點都需要設定  

圖片

###  vSphere Cloud Provider  

要先對所有 kubernetes node 做一個 taint 的設定  

```
kubectl taint node [allnode] node.cloudprovider.kubernetes.io/uninitialized=true:NoSchedule
```

#### 建立CPI configMap  

根據自己環境輸入以下資訊(VirtualCenter、datacenters)，其他可以不用更改  
```
[Global]
insecure-flag = "true"
port = "443"
secret-name = "cpi-global-secret"
secret-namespace = "kube-system"
[VirtualCenter "10.66.0.9"]
datacenters = "ZOLab-DataCenter"
```

```
kubectl -n kube-system create cm cloud-config --from-file=./vsphere.conf
```

#### 建立CPI secret  

根據自己環境輸入以下資訊(vcenter連線資訊)，其他可以不用更改  
```
apiVersion: v1
kind: Secret
metadata:
  name: cpi-engineering-secret
  namespace: kube-system
stringData:
  [x.x.x.x].username: "xxx@xx.com"
  [x.x.x.x].password: "xx"
```

```
kubectl apply -f secret.yaml
```
