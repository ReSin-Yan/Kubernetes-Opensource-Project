Last update: 2023/09/20
# Kubernetes-Opensource-Project  

以下內容會根據常用到的Kuberenete的開源專案套件  
以及常用的基本設定  
進行安裝步驟以及測試方式  
內容包含  

## Harbor  
開源專案常用的私有印象檔倉庫(private image registry)  
參考文件  
[Harbor](https://goharbor.io/docs/2.3.0/ "link")  
[容器倉庫二三四](https://blog.pichuang.com.tw/20200201-container-repos/ "link")  
包含使用https + FQDN的安裝方式  
同時也會提到如何在VMWare Tanzu的Guest Cluster上面運行  


## Prometheus  & Grafana  
Prometheus跟Grafana是社群上最多人使用的監控套件  
使用helm安裝ingress版本的kube-prometheus-stack  
安裝參考:[Kube-prometheus](https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack "link")  
使用Monitoring Stack方式  
一次安裝
 

 | 腳色 |
|-------|
| Promethues |
| Grafana    |  
| alertmanager    |


## Contour  
Contour是VMWare開發並且maintain的layer7 工具  
對比是nginx-controller  


## CICD  
CICD作為開發者很常的使用的一種開發架構  
如何透過Kuberetnes來實現CICD的環境建置  
本篇提供簡化版本的CICD環境建立  
分別使用Gitlab作為程式編寫環境  
以及Jenkins作為流程工具  
