透過moba連結ssh ip連上後 先移除docker 即使沒有也是確保之後安全
sudo yum remove docker \
                docker-client \
                docker-client-latest \
                docker-common \
                docker-latest \
                docker-latest-logrotate \
                docker-logrotate \
                docker-engine

然後開始安裝
sudo yum install -y yum-utils

sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo

sudo yum install docker-ce docker-ce-cli containerd.io -y

接著啟動Docker

sudo systemctl start docker


這是讓Docker變成常駐執行 以後重新連線就直接是Docker不用再打這些指令
sudo systemctl enable docker

檢查是否成功常駐
sudo systemctl status docker

將目前的使用者加入群組

sudo usermod -aG docker $USER

然後exit即可
-----------------------------------------------------------------------------------
1.檔案必須先關閉才能移除
docker stop jupyter
2.移除
docker rm -f jupyter
3.編輯dokcer file
vim $(pwd)/lab1/1_2/dockerfile
-----------------------------------------------------------------------------------

建立Docker下的jupyter notebook資料夾

mkdir -p jupyter/app
cd jupyter

然後下載 Docker這的jupyter 請記得這邊是Docker 所以跟原本主機安裝的不同

docker run \
--name jupyter \
-d \
-p 8888:8888 \
-p 5000:5000 \
-v $(pwd)/app:/home/jovyan/work \
jupyter/base-notebook \
start-notebook.sh --NotebookApp.token=''

連線JY  如果是用Linux環境下 ip 127.0.0.1:8888 如果是本機 則是查詢ip port還是8888

然後我們要用Flask 記得即使安裝套件  Docker下跟本機的JY都不一樣各要安裝一次
------------------------------------------------------------------------------------
用Docker連結 MySQL 一樣先建立資料夾

mkdir -p jupyter_db/sqldb
cd jupyter_db
mkdir app

然後建立網路 
docker network create test-network

查詢網路是否成功
docker network ls

下載DK下的MySQL
docker run \
--network test-network \
--name mysql \
-e MYSQL_ROOT_PASSWORD=123456 \
-v $(pwd)/sqldb:/var/lib/mysql  \
-d \
-p 3306:3306 \
mysql:latest

用JY串聯MYSQL
docker run \
--network test-network \
--name jupyter\
-d \
-p 8888:8888 \
-p 5000:5000 \
-v $(pwd)/app:/home/jovyan/work \
jupyter/base-notebook \
start-notebook.sh --NotebookApp.token=''

安裝套件
!pip install pymysql

連線
import pymysql
# 開啟資料庫連線
db = pymysql.connect(user="root", password="123456", host="mysql")
# 使用 cursor() 方法建立一個遊標物件 cursor
cursor = db.cursor()
# 使用 execute()  方法執行 SQL 查詢
cursor.execute("SELECT VERSION()")
# 使用 fetchone() 方法獲取單條資料.
data = cursor.fetchone()
print ("Database version : %s " % data)
# 關閉資料庫連線
db.close()
------------------------------------------------------------------------------------
建立網頁模板 nginx 資料夾
mkdir -p nginx/web
cd nginx

然後下載套件

docker run \
-itd \
-v $(pwd)/web:/usr/share/nginx/html \
-p 80:80 \
-p 443:443 \
nginx

再https://templated.co/ 下載網頁模板 將壓縮檔放入moba下admin/nginx/web下
輸入127.0.0.1 或是本機ip

------------------------------------------------------------------------------------
建立git clone
sudo yum install git -y

git clone https://github.com/aeon33system/docker_Advanced_ngrok.git

cd docker_Advanced_ngrok

設定環境  sudo vim /etc/vimrc
在最底下加入 
set smartindent
set tabstop=2
set shiftwidth=2
set expandtab
set number
------------------------------------------------------------------------------------

建立Line機器人 先安裝compose 如果不安裝這個 你就沒辦法獲取line加入客戶資料
sudo curl -L "https://github.com/docker/compose/releases/download/1.24.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

sudo chmod +x /usr/local/bin/docker-compose

sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose

docker-compose version

然後我們要開始編輯docker file 在這邊要進去剛剛git clone底下的cd docker_ngrok 如果沒有直接用moba資料夾點lab4
vim $(pwd)/lab4/docker-compose.yml   最後面 空格加上 -region ap  shift+:後x儲存

在lab4底層執行 docker-compose up -d

然後到google找line developers登入line創建一個官方賴

在jy開啟APP.PY 其中的 Channel secret 貼上 條碼 以及到 message api 複製 channelAccessToken
