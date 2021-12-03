# Gitlab    

以下會透過Docker的方式安裝來進行安裝Gitlab  
Gitlab作為程式碼的存放位置  
會經由專案的方式跟jenkins進行連結  
當gitlab執行設定好的動作(例如push or 更版)  
就會觸發運作指令  
接著往下執行設定好的步驟  


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
Jenkins的插件非常多  

#### 環境套件需求(必要)  
 | 配置 | 版本or建議規格 | 
|-------|-------|
| Docker | Version 17.06.0-ce+ 或著更高版本 |

#### 環境準備  

環境更新及安裝基本套件  
```
sudo apt-get update && sudo apt-get -y upgrade
sudo apt-get -y install vim build-essential curl ssh
sudo apt-get install net-tools
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

#### Gitlab建置   

建立存放資料的位置資料夾  
並將此資料夾設定環境變數  
```
mkdir gitlab
export GITLAB_HOME=/home/ubuntu/gitlab
```

接著執行docker指令來創建服務
```
sudo docker run -d   -p 443:443 -p 80:80 -p 2224:22   --name gitlab   --restart always   -v $GITLAB_HOME/config:/etc/gitlab   -v $GITLAB_HOME/logs:/var/log/gitlab   -v $GITLAB_HOME/data:/var/opt/gitlab   gitlab/gitlab-ee:latest
```

#### 開啟網頁&基礎設置  
接下來可以透過此網頁進入到Gitlab環境  
<http://ThisVMIP/>  
靜待服務開啟  
接著在指令介面輸入  
```
sudo docker exec -it gitlab grep 'Password:' /etc/gitlab/initial_root_password
```
取得登入用的密碼(預設帳號為root)
請記得再登入之後，記得去更改密碼  


#### 設定允許webhook  
Jenkins與Gitlab的連結透過webhook進行  
Gitlab新版本預設把local的功能關閉  
所以必須將此功能打開  
`Gitlab` > `Menu` > `Admin`  
左下角`Setting` > `Network` > `Outbound Request` > 將兩個都勾起  
