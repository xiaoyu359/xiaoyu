### 代码编译方法
linux编译方法
1. 安装g++、git、mongodb、build-essential
2. 安装Meteor：curl https://install.meteor.com/ | sh
3. 安装最新npm：npm i npm@latest -g
4. 克隆rocket代码：git clone https://github.com/RocketChat/Rocket.Chat.git
5. 安装依赖包
  npm run postinstall
  npm install sharp chai webpack postcss postcss-syntax fibers
  npm audit fix
6. 编译代码，启动程序：meteor npm start
  使用不同端口号编译启动：meteor run --port 3003
  编译完成的文件在.meteor/local/build文件夹下，打包压缩后可发布

### centos7 安装方法
[参照网址](http://blog.poetries.top/handbook/html/服务器/centos/Rocket.Chat.html)
1. 安装依赖
  yum -y install epel-release && yum -y update
  yum install -y curl
2. 安装node和npm
  yum install -y nodejs npm
  _node版本很重要需要安装`n`来切换版本_
 npm install -g inherits n
 _切换node版本, 很重要_
 n 4.8.4
3. 安装meteor
  curl https://install.meteor.com | sh
4. 安装mongodb
  1) 安装使用Mongodb，先添加 yum repo
     vi /etc/yum.repos.d/mongodb.repo
  2) 复制下面内容，保存并退出:wq
     [mongodb]
     name=MongoDB Repository
     baseurl=http://downloads-distro.mongodb.org/repo/redhat/os/x86_64/
     gpgcheck=0
     enabled=1
  3) 安装图形库以及Mongodb数据库
      yum install -y GraphicsMagick mongodb-org-server mongodb-org gcc-c++
  4) 提前配置数据库
     启动MongoDB
     service mongod start
     连接MongoDB
     mongo
     use rocketchat
     exit
     重启数据库
     service mongod restart
5. 安装Rocket.Chat
   `cd /opt`
   `curl -L https://download.rocket.chat/stable -o rocket.chat.tgz`
  `# 解压 rocket.chat.tgz`
  `tar zxvf rocket.chat.tgz`
   `mv bundle Rocket.Chat`
   `cd Rocket.Chat/programs/server`
   `npm install`
   `cd ../..`
6. 直接在命令行中运行下面命令，配置 PORT, ROOT_URL 和 MONGO_URL:
   export PORT=3000
   export ROOT_URL=http://127.0.0.1:3000/
   export MONGO_URL=mongodb://localhost:27017/rocketchat
   _**将3000替换为您选择的端口，一般情况默认就好。 如果您选择使用端口80，则需要以root身份运行Rocket.Chat。 如果您没有配置DNS，请使用您的IP代替主机名。 您可以稍后在管理员菜单中进行更改。**_
7. 启动服务
  chkconfig mongod on
  systemctl start mongod
  node main.js
  meteor npm install --save bcrypt
  _使用上面的连接地址 http://127.0.0.1:3000/在浏览器中打开_
8. 添加开机启动
`vi /usr/lib/systemd/system/rocketchat.service`
将下面内容复制到上面文件中。
[Unit]
Description=The Rocket.Chat server
After=network.target remote-fs.target nss-lookup.target nginx.target mongod.target
[Service]
ExecStart=/usr/local/bin/node /opt/Rocket.Chat/main.js
StandardOutput=syslog
StandardError=syslog
SyslogIdentifier=rocketchat
User=root
Environment=MONGO_URL=mongodb://localhost:27017/rocketchat ROOT_URL=http://your-host-name.com-as-accessed-from-internet:3000/ PORT=3000
[Install]
WantedBy=multi-user.target
现在可以运行以下命令来启用此服务：
systemctl enable rocketchat.service
最后通过运行来启动它，你就不需要：
systemctl start rocketchat.service
