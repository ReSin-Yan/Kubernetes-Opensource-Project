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
| Cloud Provider Interface    |  
| alertmanager    |


## 事先準備  

 | 事先需求 |
|-------|
| kubernetes 叢集(版本1.28.2) |
| 安裝在vsphere上的native kubernetes  |  



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

 | 虛擬機參數 | 數值  |
|-------|-------|
| disk.EnableUUID | TRUE |    

圖片

###  vSphere Cloud Provider  

要先對所有 kubernetes node 做一個 taint 的設定  

```
kubectl taint node [allnode] node.cloudprovider.kubernetes.io/uninitialized=true:NoSchedule
```

#### 下載設定檔案 

根據目標環境的kubernetes版本來設定  
```
VERSION=1.28
wget https://raw.githubusercontent.com/kubernetes/cloud-provider-vsphere/release-$VERSION/releases/v$VERSION/vsphere-cloud-controller-manager.yaml
```

#### 編輯CPI secret  

編輯vsphere-cloud-controller-manager.yaml這一之檔案  
並且根據自己環境輸入以下資訊(vcenter連線資訊)  
安裝過程中，我修改了IP以及帳號密碼  
```
stringData:
  10.66.0.9.username: "xxx"
  10.66.0.9.password: "xxx"
```

```
apiVersion: v1
kind: Secret
metadata:
  name: vsphere-cloud-secret
  labels:
    vsphere-cpi-infra: secret
    component: cloud-controller-manager
  namespace: kube-system
  # NOTE: this is just an example configuration, update with real values based on your environment
stringData:
  10.66.0.9.username: "xxx"
  10.66.0.9.password: "xxx"
```

#### 編輯CPI ConfigMap  

根據以下Sample，修改成自己環境的內容  
以下是我有修改的部分  
並且將region部分刪除  

```
    # vcenter section
    vcenter:
      10.66.0.9:
        server: 10.66.0.9
        user: xxxx
        password: xxxx
        datacenters:
          - ZOLab-DataCenter
```

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: vsphere-cloud-config
  labels:
    vsphere-cpi-infra: config
    component: cloud-controller-manager
  namespace: kube-system
data:
  # NOTE: this is just an example configuration, update with real values based on your environment
  vsphere.conf: |
    # Global properties in this section will be used for all specified vCenters unless overriden in VirtualCenter section.
    global:
      port: 443
      # set insecureFlag to true if the vCenter uses a self-signed cert
      insecureFlag: true
      # settings for using k8s secret
      secretName: vsphere-cloud-secret
      secretNamespace: kube-system

    # vcenter section
    vcenter:
      10.66.0.9:
        server: 10.66.0.9
        user: vmadmin@zodemo.com
        password: zeroneP@ssw0rd01
        datacenters:
          - ZOLab-DataCenter
```
#### 部屬檔案  

```
kubectl apply -f vsphere-cloud-controller-manager.yaml
```

