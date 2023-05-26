# Build-server    

以下會透過Docker的方式安裝來進行安裝build-server  
主要會透過dockerfiles的方式來把安裝的東西打包成images  


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
sudo apt-get install net-tools default-j git

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
