Last update: 2021/7/23
# Kubernetes-Opensource-Project  


本分內容會根據常用到的Kuberenete的開源專案套件進行安裝步驟以及測試方式  
內容包含  

## Harbor  
參考文件:[Harbor](https://goharbor.io/docs/2.3.0/ "link")  
包含使用https + FQDN的安裝方式  
同時也會提到如何在VMWare Tanzu的Guest Cluster上面運行  


## Prometheus  & Grafana  
使用helm安裝ingress版本的kube-prometheus-stack  
安裝參考:[Kube-prometheus](https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack "link")  
使用Monitoring Stack方式  
一次安裝
 

 | 腳色 |
|-------|
| Promethues |
| Grafana    |  
| alertmanager    |


## Velero  

## Contoour
