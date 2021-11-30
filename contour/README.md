# Contour安裝方式

使用helm安裝Contour  
Contour是由VMware提出並維護的開源套件
如果需要使用本身是不用收費  
但是如果需要到enterprise等級的support  
或是進階的錯誤排除，就會需要額外購買support  

 

 | 套件名稱 | 角色  |
|-------|-------|
| MetalLB | L4 loadbalancer |  
| Contour | L7 Ingress |  


## 事先準備  

 | 事先需求 |
|-------|
| helm |
| kubernetes|  


## 安裝步驟  
  
### 沒有LoadBalance的安裝方式  
由於沒有LoadBalance  
所以需要自己額外安裝此項功能  
這邊採用同為opensource的metalLB  

```
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.10.0/manifests/namespace.yaml
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.10.0/manifests/metallb.yaml
```

透過以上兩行進行metalLB的安裝(版本為0.10)  

接下來需要設定給定的IP Range(今天服務用LoadBalance的方式建立出來之後，會去拿一個IP)  
給定的這段IP會分配給建立出來的服務  

```
vim configIPrange.yaml
```

貼入以下內容
```
apiVersion: v1
kind: ConfigMap
metadata:
  namespace: metallb-system
  name: config
data:
  config: |
    address-pools:
    - name: default
      protocol: layer2
      addresses: [startIP] - [endIP]
```

```
kubectl apply -f configIPrange.yaml
```

本資料夾內有放置相關的執行腳本，可以直接下載來使用  



加入repo&進行升級  
```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts  
helm repo update
```

建立放置安裝內容的命名空間  
```
kubectl create ns monitor  
```

