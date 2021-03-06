# worknode    

worknode代表要讓jenkins放一個控制agent近來的節點  
會透過lable的方式，讓特定節點執行特定步驟  
例如指定lable為build，並且在pipeline中設定  
建立images的時候指令build的節點執行  


這邊的步驟是讓節點作為Tanzu Kubernetes Cluster的控制節點(kubectl)  
如果本來環境不是Tanzu Kubernetes Cluster的話，可以少做對應的步驟  

## 安裝步驟  

#### 環境硬體配置(建議需求)  
使用的環境為  
 | 配置 | 版本or建議規格 | 
|-------|-------|
| OS | ubutu desktop 20.04 |
| CPU |  4 CPU |
| Memory  | 4 GB+ |
| Disk  | 50 GB+ |  

以上皆為建議配置  
建議可以設置稍微大一點的規格  

#### 環境套件需求(必要)  
 | 配置 | 版本or建議規格 | 
|-------|-------|
| Docker | Version 17.06.0-ce+ 或著更高版本 |

#### 環境準備  

環境更新及安裝基本套件  
```
sudo apt-get update && sudo apt-get -y upgrade
sudo apt-get -y install vim build-essential curl ssh
sudo apt-get install net-tools default-jdk git

mkdir jenkins
sudo chmod +777 jenkins/

```

安裝Docker engine    
```
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh
```

確認安裝版本
```
sudo docker --version
```

#### Tanzu  
Tanzu Kubernetes Cluster需要確認環境可以成功連結到TKC環境並且有部屬服務的能力  
  
  
