# Jenkins    

以下會透過Docker的方式安裝來進行安裝Jenkins  
利用Docker方式可以省掉非常多步驟  


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

## Jenkins安裝  

#### 環境準備  
第一步需要建立放置Jenkins啟動之後  
放置一些相關文件及記錄檔的位置  
```
mkdir jenkins_home
```

接著需要將此資料夾進行設定  
分別設置此資料夾的權限  
以及誰可以讀取(jenkins的images是預設跑root權限，所以在設定時要root權限給此資料夾)  
```
sudo chmod +777 jenkins_home
sudo chown -R 1000:1000 jenkins_home/
```

接著執行部屬指令  
注意這邊是需要jdk8版本的jenkins，因為此環境預計與gitlab連結，所以會需要jdk8版本  
如果連結其它環境則不需要  
- ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+) 
注意路徑/home/ubuntu/jenkins_home，請改成自己環境的路徑  
  
```
sudo docker run --name jenkins -p 8080:8080 -p 50000:50000 -v /home/ubuntu/jenkins_home:/var/jenkins_home -v /var/run/docker.sock:/var/run/docker.sock jenkins/jenkins:lts-jdk8
```
#### 開啟網頁&基礎設置  
接下來可以透過此網頁的IP:8080進入到jenkins環境  
<http://ThisVMIP:8080/>  
靜待服務開啟  

接著會請您輸入啟動的token  
啟動token可以在兩個地方找到  
第一種方式如下圖，可以在執行的地方視窗找到  
圖片  

第二種方式需要另外開ssh連線  
```
cat jenkins_home/secrets/initialAdminPassword
```

將獲得到的Token輸入  
![img](https://github.com/ReSin-Yan/Kubernetes-Opensource-Project/blob/main/CICD/Jenkins/cicd/input%20token.PNG)   

接著選擇左邊安裝建議插件(之後會再補其它的插件)  
![img](https://github.com/ReSin-Yan/Kubernetes-Opensource-Project/blob/main/CICD/Jenkins/cicd/install%20suggested%20plugin.PNG)   

等待安裝完成

建立admin帳號  
![img](https://github.com/ReSin-Yan/Kubernetes-Opensource-Project/blob/main/CICD/Jenkins/cicd/creat%20admin.PNG)   

設定URL  
保持預設即可  
![img](https://github.com/ReSin-Yan/Kubernetes-Opensource-Project/blob/main/CICD/Jenkins/cicd/set%20URL.PNG)   

以上到這邊基本安裝完成  


## Jenkins環境設定  
Jenkins基本安裝完成之後  
還需要額外設定  
分別為  
`插件安裝`  
跟  
`加入節點`


#### 插件安裝  

`Dashboard` >  `Manage Jenkins` > `Manage Plugins` > `Available`
依序搜尋這些套件並勾起  
之後點選Install without restart  
Gitlab  
Gitlab Webhook  
Gitlab authentication  
generic webhook trigger  

ssh  
Public over ssh  
ssh agent  
ssh pipline steps  
![img](https://github.com/ReSin-Yan/Kubernetes-Opensource-Project/blob/main/CICD/Jenkins/cicd/plugin.PNG)   

#### 新增節點

`Dashboard` >  `Manage Jenkins` > `Manage Nodes and Clouds` 左邊點選 `New Node`  
輸入Node name  
選擇Permanent Agent  

![img](https://github.com/ReSin-Yan/Kubernetes-Opensource-Project/blob/main/CICD/Jenkins/cicd/addnode1.PNG)   



 | 項目 | 輸入資訊 | 
|-------|-------|
| Name | 好分辨的即可 |
| Remote root directory | /home/ubuntu/jenkins  (如果其他節點的安裝步驟有按照其它的安裝步驟) |
| labels | 目前此node的角色，會影響之後在寫pipline的設定 |
| launch method | Launch agents via ssh |
| Host | 連線機器的IP |

![img](https://github.com/ReSin-Yan/Kubernetes-Opensource-Project/blob/main/CICD/Jenkins/cicd/addnode2.PNG)   


Credentials 點選Add  
選擇使用Username 跟 Password  
並且輸入  
在Jenkins內，是以憑證方式儲存，如果其它node的帳號密碼相同  
那可以透過同一組憑證來使用  

![img](https://github.com/ReSin-Yan/Kubernetes-Opensource-Project/blob/main/CICD/Jenkins/cicd/addnode4.PNG)   



 | 項目 | 輸入資訊 | 
|-------|-------|
| Host Key Verofocatopn Strategy | Non verifiying Verification Strategy |

其它項目保持預設就可以了  

![img](https://github.com/ReSin-Yan/Kubernetes-Opensource-Project/blob/main/CICD/Jenkins/cicd/addnode3.PNG)   
