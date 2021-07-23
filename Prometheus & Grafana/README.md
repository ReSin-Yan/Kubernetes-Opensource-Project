# 使用helm安裝ingress版本的kube-prometheus-stack

使用helm安裝ingress版本的kube-prometheus-stack  
安裝參考:[Kube-prometheus](https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack "link")  
使用Monitoring Stack方式  
一次安裝

Promethues  
Grafana  
alertmanager  

這三個角色  

## 事先準備  
環境需要事先準備包含  
helm,版本建議>3.0  
可以通的ingress  
StorageClass  


## 安裝步驟  
  
加入repo&進行升級  
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts  
helm repo update

建立放置安裝內容的命名空間  
kubectl create ns monitor  

對需要監控的節點打上lable  
kubectl get node

NAME          STATUS   ROLES                  AGE   VERSION
mandy-k8s01   Ready    control-plane,master   22d   v1.21.2
mandy-k8s02   Ready    <none>                 22d   v1.21.2
mandy-k8s03   Ready    <none>                 22d   v1.21.2  

例如我的全部的節點都需要被監控
kubectl label node mandy-k8s01 category=monitoring
kubectl label node mandy-k8s02 category=monitoring
kubectl label node mandy-k8s03 category=monitoring

## 檢查 values.yaml  
需要修改四個部分  
