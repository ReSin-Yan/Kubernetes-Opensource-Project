# Harbor  
以下提供Https + FQDN的 Harbor安裝模式  

## 安裝步驟  

#### 環境硬體配置(建議需求)  
使用的環境為  
OS      :ubutnu desktop 20.04  
CPU     :4 CPU  
Memory  :8 GB  
Disk    :200GB  

#### 環境套件需求(必要)  
Docker engine   : Version 17.06.0-ce+ 或著更高版本  
Docker Compose  : Version 1.18.0 或著更高版本  
Openssl         : 最新版本  

(在ubutnu20版本不需要額外安裝)  

#### 環境準備  

環境更新及安裝基本套件  
```
sudo apt-get udpate && sudo apt-get -y upgrade
sudo apt-get -y install vim build-essential curl ssh
sudo apt-get install net-tools
```

安裝Docker engine    
```
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh

#確認安裝版本
sudo docker --version
```

安裝Docker Compose    
```
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

#確認安裝版本
sudo docker-compose --version
```

### 憑證準備  

#### 建立放資料夾  
憑證將會放到此資料夾，接下來harbor的安裝包也會下載到這邊  
```
mkdir harbor
cd harbor/
```

#### 產生由認證機構發布的證書  

建立本地https憑證  
其中`<YourFQDN>`整段換成自己的FQDN  
參數內部是包含時區...等資訊  
參數可以跟自行修改或是按照步驟中設定即可  




```
#生成 CA key
openssl genrsa -out ca.key 4096
#生成 CA 憑證
openssl req -x509 -new -nodes -sha512 -days 3650 -subj  "/C=CN/ST=Beijing/L=Beijing/O=example/OU=Personal/CN=<YourFQDN>.com" -key ca.key -out ca.crt
```


#### 生成 server的憑證  
憑證通常包含一個 .crt 文件和一個 .key 文件  

```
#生成私有金鑰
openssl genrsa -out <YourFQDN>.com.key 4096
#生成certificate signing request(CSR)
openssl req -sha512 -new -subj "/C=CN/ST=Beijing/L=Beijing/O=example/OU=Personal/CN=<YourFQDN>.com" -key <YourFQDN>.com.key -out <YourFQDN>.com.csr
```


#### 建立 x509 v3 擴展文件  

無論是使用 FQDN 還是 IP 地址連接到Harbor  
都必須創建此文件，讓Harbor主機產生符合(SAN)和x509 v3的憑證要求  
替換DNS.1,DNS.2,DNS.3  
```
cat > v3.ext <<-EOF
authorityKeyIdentifier=keyid,issuer
basicConstraints=CA:FALSE
keyUsage = digitalSignature, nonRepudiation, keyEncipherment, dataEncipherment
extendedKeyUsage = serverAuth
subjectAltName = @alt_names
[alt_names]
DNS.1=<YourFQDN>.com
DNS.2=<YourFQDN>
DNS.3=<VM-Hostname>
EOF
```

#### 使用v3.ext文件為Harbor生成憑證  

```
openssl x509 -req -sha512 -days 3650 -extfile v3.ext -CA ca.crt -CAkey ca.key -CAcreateserial -in <YourFQDN>.com.csr -out <YourFQDN>.com.crt
```

#### 將憑證轉換為Docker所需要的cert格式  
```
openssl x509 -inform PEM -in <YourFQDN>.com.crt -out <YourFQDN>.com.cert
```

### Harbor安裝  

#### 使用離線安裝包  
版本為2.2.3  
解壓縮之後進入資料夾
```
wget https://github.com/goharbor/harbor/releases/download/v2.2.3/harbor-offline-installer-v2.2.3.tgz
tar -zxvf harbor-offline-installer-v2.2.3.tgz
cd harbor/
```

#### 複製及編輯設定檔  
需要修改兩個部分  
hostname  
https的certificate跟private_key  
```
cp harbor.yml.tmpl harbor.yml
vim harbor.yml 
```
編輯完成之後如圖  
我測試用的FQDN為resinharbor.com  
圖片  

#### 安裝  

```
sudo ./install.sh
```

