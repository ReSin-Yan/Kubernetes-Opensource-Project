Last update: 2021/9/20
# CICD簡述  

## 環境準備以及說明  

### Jenkins  
Jenkins是一種開源的CI&CD應用軟體，用於自動化各種任務，其中包括建置、測試和部屬應用。    
Jenkins支援各種運作方式，可以通過底層系統、Docker或著通過Java Program來運行。  


### Gitlab  
與本文所在的位置Github類似   
使用雲端平台的GitHub，需要將程式馬上傳，如果設為私有會需要額外收費。  
GitLab就是完全免費的(社群版免費，企業版需要定月)  
能夠瀏覽程式碼，管理BUG和註解，適合再團隊內部使用。  

### Harbor  
安裝步驟參考下列連結  
[Harbor](https://github.com/ReSin-Yan/Kubernetes-Opensource-Project/tree/main/Harbor "link")  

### Build-server  
在此環境設置中  
需要有一個環境用來建立容器images    
並且在建立完成之後將images推送到Harbor容器倉庫  

## 環境設定  

### 建立Gitlab專案並且與Jenkins連結  
透過Gitlab的機制  
與Jenkins進行連結  
目的是在當Gitlab的專案進行任何形式的更新時  
驅動寫在Jenkins的運作腳本  

#### Gitlab 建立新專案  
進到Gitlab頁面  
點選`New Project` >  `Create blank project`  
依序輸入`Project Name`並把Visibility Level調整成`Public`  > 點選`Create Project`  
並把本專案內的gitlabprojectfile資料夾內全部的東西全都放進去  

接下來還需要設定Gitlab可以接Local的網路空間  
`Menu` >  `Admin`  >  左方 `Setting` > `Network`  
在Outbound requests欄位，點選Expand  
新增勾選Allow requests to the local network from web hooks and services  
之後儲存離開  
![img](https://github.com/ReSin-Yan/Kubernetes-Opensource-Project/blob/main/CICD/img/jenkinsetting4.PNG)   

#### Jenkins 建立新專案並且與Gitlab進行連結  
進到Jenkins頁面  .
點選`New Item` > `點選pipline並且輸入名稱` (名稱建議有意義)  `OK`  

建立之後再 Build Triggers打勾 
Build when a change is pushed to GitLab. GitLab webhook URL:xxxxxxxxxxxxx  
![img](https://github.com/ReSin-Yan/Kubernetes-Opensource-Project/blob/main/CICD/img/jenkinsetting1.PNG)   
注意要將URL複製下來接下來會將此貼到Gitlab的設定內  
右下角 `Advanced` > 拉到最下面有一個 `Secret token` 點選Generate  
![img](https://github.com/ReSin-Yan/Kubernetes-Opensource-Project/blob/main/CICD/img/jenkinsetting2.PNG)   

將以上兩個資訊記錄下來  
在Gitlab頁面，進入到project內後  
點選左邊`Setting` > `Webhooks` 依序輸入`URL` 和 `Secret token`  
![img](https://github.com/ReSin-Yan/Kubernetes-Opensource-Project/blob/main/CICD/img/jenkinsetting3.PNG)   
`Add webhook`  
可以點選測試來測試看看連結是否成功  

回到jenkins 專案的Dashboard，如果可以看到左下角自動跑出build history及代表成功連結  
![img](https://github.com/ReSin-Yan/Kubernetes-Opensource-Project/blob/main/CICD/img/jenkinsetting5.PNG)   


#### 決定Jenkins pipline是寫在Jenkins or Gitlab  
這段是可以自己決定的  
本篇使用的方式為將檔案一起放入Gitlab內  
所以需要額外設定兩個步驟  
1.將pipline檔案(Jenkinsfiles)放入Gitlab  
2.在jenkins內部設定預設執行的腳本從Gitlab內搜尋  

#### 新增Kubernetes工作節點  
這邊使用的方式是直接把kuberenets的控制節點加進來  
所有的節點設置方式都如同buildserver  
安裝步驟也相同  
參考連結  
Jenkins add node  
buildserver設定  


#### 將pipline檔案放入Gitlab  
在gitlab project內新增檔案  
點選檔案上的`+`  > `New file` > 名子輸入 `Jenkinsfile` (記住此名稱，需要跟在Jenkins那邊設定相同)  


接著貼上  

```
pipeline {
  agent none 
  stages {
    stage("Build image"){
      agent {label "buildserver"}
      steps{
        sh """
          ls
        """
      }
    }
  }
}
```

點選最下面的commit changes，注意這邊還未設定與jenkins連結  
所以執行jenkins雖然會有反應，但是本身還未寫上任何的腳本文件，只會空跑  

#### 在jenkins內部設定預設執行的腳本從Gitlab內搜尋  
回到Jenkins，點選 `Configure` > `pipeline`  
分別設定  
Definition > `pipeline script form SCM`  
SCM > `Git`  
Repository > `你的gitlab project URL`，注意要放最乾淨的路徑  
Branch specifier > `*/*`  
Script Path > `Jenkinsfile`  
![img](https://github.com/ReSin-Yan/Kubernetes-Opensource-Project/blob/main/CICD/img/jenkinsetting6.PNG)   

接著可以在Gitlab測試是否連結成功  
隨便更改一下jenkinsfiles，或是新檔案都可以  



#### 測試jenkinsfilev1  
打開jenkinsfilev1，將內容貼到原本的pipeline上面去覆蓋掉  
此步驟的目的是驗證環境是否成功連結到三台機器上面並且執行對應的指令  

#### 測試jenkinsfilev2  
這邊需要先行安裝好Harbor  
接著會透過v2檔案確認是否有成功使用harbor上面的檔案

如果底層的k8s環境是Tanzu，需要額外設定到harbor的連結  
參考連結  

如果環境是一般kuberenets或是其他kuberenets的環境，需要確認在向harbor拉印象檔的時候是正常沒有問題的  
一般kuberenets參考連結  


注意需要更改檔案的FQDN名稱  


#### 正式執行CICD腳本  
如果執行v2是成功的代表環境已經成功聯接  
如果有error的產生，問題就會發生在目的端  
例如無法連線到harbor，或是憑證錯誤之類的問題  



