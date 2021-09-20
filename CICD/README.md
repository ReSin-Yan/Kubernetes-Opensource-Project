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
依序輸入`Project Name`並把Visibility Level調整成`Public`  
