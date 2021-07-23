# 使用helm安裝ingress版本的kube-prometheus-stack

使用helm安裝ingress版本的kube-prometheus-stack  
安裝參考:[Kube-prometheus](https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack "link")  
使用Monitoring Stack方式  
一次安裝
 

 | 腳色 |
|-------|
| Promethues |
| Grafana    |  
| alertmanager    |


## 事先準備  

 | 事先需求 |
|-------|
| helm |
| StorageClass    |  
| ingress    |


## 安裝步驟  
  
加入repo&進行升級  
```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts  
helm repo update
```

建立放置安裝內容的命名空間  
```
kubectl create ns monitor  
```

對需要監控的節點打上lable  
```
kubectl get node



NAME          STATUS   ROLES                  AGE   VERSION
mandy-k8s01   Ready    control-plane,master   22d   v1.21.2
mandy-k8s02   Ready    <none>                 22d   v1.21.2
mandy-k8s03   Ready    <none>                 22d   v1.21.2  

```

例如我的全部的節點都需要被監控
```
kubectl label node mandy-k8s01 category=monitoring
kubectl label node mandy-k8s02 category=monitoring
kubectl label node mandy-k8s03 category=monitoring
```

## 檢查 values.yaml  
修改此附檔的values.yaml  
需要修改幾個部分  
Alertmananger的FQDN   
![img](https://github.com/ReSin-Yan/DGX-Demo/blob/main/img/prometheusConfig%E7%AF%84%E4%BE%8B.png)  
Alertmananger的SC名稱(根據自己的SC命名)  
![img](https://github.com/ReSin-Yan/DGX-Demo/blob/main/img/prometheusConfig%E7%AF%84%E4%BE%8B.png)  
grafana的FQDN  
![img](https://github.com/ReSin-Yan/DGX-Demo/blob/main/img/prometheusConfig%E7%AF%84%E4%BE%8B.png)  
prometheus的FQDN  
![img](https://github.com/ReSin-Yan/DGX-Demo/blob/main/img/prometheusConfig%E7%AF%84%E4%BE%8B.png)  
prometheus的StorageClass  
![img](https://github.com/ReSin-Yan/DGX-Demo/blob/main/img/prometheusConfig%E7%AF%84%E4%BE%8B.png)  


