[TOC]

[openvpn官网](https://openvpn.net/)

[openvpn文档](https://openvpn.net/vpn-server-resources/)

[openvpn客户端](https://openvpn.net/vpn-client/)

[openvpn github仓库](https://github.com/OpenVPN/openvpn3.git)

[libreswan](https://www.920430.com/archives/2871832855.html)

[docker libreswan](https://www.updateweb.cn/chunxu/item-137.html)

[两云互通](https://blog.csdn.net/weixin_43423965/article/details/105071519)

# 部署VPN服务器端

实现办公网络连接到云资源

[部署vpn](https://mp.weixin.qq.com/s/jPjkxrhqG_6OHCRtCev8EQ)

## 部署Docker

    yum remove docker docker-common docker-selinux docker-engine -y
    yum install -y yum-utils device-mapper-persistent-data lvm2 -y
    yum-config-manager --add-repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
    yum install docker-ce -y
    systemctl start docker
    systemctl enable docker

## 部署VPN

注：IP国内被封（停止EC2更换IP），更换route53域名对应公网IP;重装只需运行新容器

MAC刷新DNS\:sudo killall -HUP mDNSResponder && echo macOS DNS Cache Reset
### 拉取镜像

    docker pull kylemanna/openvpn

### 设置全局变量
    OVPN_DATA="data/openvpn"
    IP="0.tcp.ap.ngrok.io"  # 公网IP或域名
### 创建文件目录
    mkdir -p ${OVPN_DATA}
### 生成配置文件
    docker run -v ${OVPN_DATA}:/etc/openvpn --rm kylemanna/openvpn ovpn_genconfig -u udp://${IP} -s 11.8.8.0/24 -r 11.8.8.1/24 -n 223.5.5.5 -n 223.6.6.6 -n 
    # 参数解释
    # -u 指定服务器公网地址
    # -s 分配给客户端的子网IP，默认是192.168.255.0/24
    # -r 路由地址，默认是192.168.254.0/24
    # -n DNS地址，可以多个，默认是8.8.8.8和8.8.4.4
### 生成密钥文件
```bash
docker run -v ${OVPN_DATA}:/etc/openvpn --rm -it kylemanna/openvpn ovpn_initpki
Enter PEM pass phrase: 输入密码（你是看不见的） 
Verifying - Enter PEM pass phrase: 输入密码（你是看不见的） 
Common Name (eg: your user, host, or server name) [Easy-RSA CA]:回车一下 
Enter pass phrase for /etc/openvpn/pki/private/ca.key:输入上边的密码
Enter pass phrase for /etc/openvpn/pki/private/ca.key:输入上边的密码
```
### 运行docker容器
```bash
docker run --name openvpn --restart=always -v ${OVPN_DATA}:/etc/openvpn -d -p 1194:1194/udp --privileged kylemanna/openvpn
# 物理机上的端口按自己需求更改
docker run  -v ${OVPN_DATA}:/etc/openvpn -d -p 1194:1194/udp --restart=always --name openvpn --cap-add=NET_ADMIN --sysctl net.ipv6.conf.all.disable_ipv6=0 --sysctl net.ipv6.conf.default.forwarding=1 --sysctl net.ipv6.conf.all.forwarding=1  kylemanna/openvpn 
```
### 安全组放行端口
## 签发证书和撤销证书
### 创建用户
```bash
docker run -v ${OVPN_DATA}:/etc/openvpn --rm -it kylemanna/openvpn easyrsa build-client-full your-client-name nopass
Enter pass phrase for /etc/openvpn/pki/private/ca.key:输入上边的密码
```

### 签发客户端证书

```bash
# 创建client目录，将用户证书统一放至此目录
mkdir ${OVPN_DATA}/client
docker run -v ${OVPN_DATA}:/etc/openvpn --rm kylemanna/openvpn ovpn_getclient your-client-name > ${OVPN_DATA}/client/your-client-name.ovpn
```
### 撤销签署的证书（删除用户）
```bash
# 进入docker容器
docker exec -it openvpn bash
# 一下命令在容器内执行
easyrsa revoke your-client-name
easyrsa gen-crl 
cp /etc/openvpn/pki/crl.pem /etc/openvpn/crl.pem
# 编辑openvpn配置文件
vim /etc/openvpn/openvpn.conf
# 添加以下内容
crl-verify /etc/openvpn/crl.pem

# 重启docker容器
docker restart openvpn
```

### 脚本实现

为了方便管理，将签发证书和撤销证书写成脚本

#### 指量创建用户

```bash
yum install -y expect

#userlist 用户列表

#!/bin/bash
# openvpn_useradd.sh

password='wsq3zsQ!'
for NAME in `cat userlist`;do
expect <<EOF
#read -p "please your username: " NAME
    spawn docker exec -it openvpn easyrsa build-client-full $NAME nopass
    expect "ca.key:" {send "$password\n"}
    expect eof
EOF
docker exec -ti openvpn ovpn_getclient $NAME > /data/openvpn/client/"$NAME".ovpn
sed -i "s|1194|1688|g" /data/openvpn/client/$NAME.ovpn
cp /data/openvpn/client/$NAME.ovpn /tmp/aws_test-$NAME.ovpn
done
```

#### 签发证书

    cat openvpnCreateUser.sh
    #!/bin/bash
    read -p "please your username: " NAME
    docker run -v /data/openvpn:/etc/openvpn --rm -it kylemanna/openvpn easyrsa build-client-full $NAME nopass
    docker run -v /data/openvpn:/etc/openvpn --rm kylemanna/openvpn ovpn_getclient $NAME > /data/openvpn/client/"$NAME".ovpn

#### 批量删除用户

    #!/bin/bash
    # openvpn_userdel.sh
    #!/bin/bash
    password='wsq3zsQ!'
    for DNAME in `cat userlist`;do
    expect <<EOF
        spawn docker exec -ti openvpn easyrsa revoke $DNAME
        expect {
            "revocation:" { send "yes\n";exp_continue }
            "ca.key:" { send "$password\n" }
        }
        expect eof
        spawn docker exec -ti openvpn easyrsa gen-crl
        expect "ca.key:" { send "$password\n"}
        expect eof
    EOF
    docker exec -ti openvpn rm -f /etc/openvpn/pki/reqs/"$DNAME".req
    docker exec -ti openvpn rm -f /etc/openvpn/pki/private/"$DNAME".key
    docker exec -ti openvpn rm -f /etc/openvpn/pki/issued/"$DNAME".crt
    docker exec -ti openvpn rm -f /etc/openvpn/conf/"$DNAME".ovpn
    docker exec -ti openvpn rm -rf /etc/openvpn/clients/"$DNAME"
    done

#### 撤销证书

```

cat openvpnRevokeUser.sh
#!/bin/bash
read -p "please enter username you want revoke: " NAME
docker run -v /data/openvpn:/etc/openvpn --rm -it kylemanna/openvpn easyrsa revoke $NAME
docker run -v /data/openvpn:/etc/openvpn --rm -it kylemanna/openvpn easyrsa gen-crl 
docker run -v /data/openvpn:/etc/openvpn --rm -it kylemanna/openvpn cp /etc/openvpn/pki/crl.pem /etc/openvpn/crl.pem
docker restart openvpn

# 或者 （以下方法不用更改配置文件，即不用添加'crl-verify /etc/openvpn/crl.pem'）
#!/bin/bash
read -p "please enter username you want revoke: " NAME
docker run -v /data/openvpn:/etc/openvpn --rm -it kylemanna/openvpn easyrsa revoke $NAME
docker run -v /data/openvpn:/etc/openvpn --rm -it kylemanna/openvpn easyrsa gen-crl
docker run -v /data/openvpn:/etc/openvpn --rm -it kylemanna/openvpn rm -f /etc/openvpn/pki/reqs/"$NAME".req
docker run -v /data/openvpn:/etc/openvpn --rm -it kylemanna/openvpn rm -f /etc/openvpn/pki/private/"$NAME".key
docker run -v /data/openvpn:/etc/openvpn --rm -it kylemanna/openvpn rm -f /etc/openvpn/pki/issued/"$NAME".crt
docker restart openvpn
```

### 查看连接用户列表

    docker exec openvpn ovpn_status

## 开放VPN注册

[gunicorn使用说明](https://zhuanlan.zhihu.com/p/88422780)

### 安装

```shell
apt install -y expect
pip install greenlet # 使用异步必须安装
pip install eventlet # 使用eventlet workers
pip install gevent   # 使用gevent workers
pip install gunicorn
```

### 用户注册接口

实现注册接口和发钉钉

```python
cat app.py
# !/usr/bin/python3
# -*- coding:utf-8 -*-
import os
from flask import Flask, request, jsonify
import json,requests
app = Flask(__name__)

dingding_token ='9c93902a071c5de3df0a4a4824fc87616528ac279a86ecbafa65177a99465490'
#淡绿色字体，并@对应的人
def send_green(email_address):
    url = f"https://oapi.dingtalk.com/robot/send?access_token={dingding_token}"
    headers = {
        'Content-Type': 'application/json',
    }
    data = {
            "msgtype": "markdown",
            "markdown": {
                "title": f"VPN User",
                "text": f"<font face='黑体' color='#00EC00'>{email_address} Create test vpn Success</font>"
            }
        }
    response = requests.post(url, headers=headers, data=json.dumps(data))
    print(response.text)

def check_email_url(email_address):
    # check '@'
    at_count = 0
    for element in email_address:
        if element == '@':
            at_count = at_count + 1

    if at_count != 1:
        return 0

    # check ' '
    for element in email_address:
        if element == ' ':
            return 0

    # check 'ihoment.com'
    postfix = email_address.endswith("ihoment.com")
    if not postfix:
        return 0

    # check char
    for element in email_address:
        if element.isalpha() == False and element.isdigit() == False:
            if element != '.' and element != '@' and element != '_':
                return 0

    return 1

@app.route("/ping", methods=['get'])
def ping_demo():
    return jsonify({"message": "", "status": 200})
    
@app.route('/vpn/createuser', methods=['POST'])
def creat_vpn_user():
    print("enter...")
    if request.method == 'POST':
        if not request.data:  # 检测是否有数据
            return jsonify({"error": "data params must need"})
        ret =json.loads(request.data)
        sum=check_email_url(ret["email_addr"])
        with  open("./emails.txt","w") as f:
            f.write(ret["email_addr"])
        if sum == 1:
            cmd = 'sh main.sh'
            os.system(cmd)
            send_green(ret["email_addr"])
            return jsonify({"info msg":"Please check the email"})
        return jsonify({"error msg": "The information entered in the mailbox is incorrect. Please check it"})

    else:
        return jsonify({"error msg": "Only support POST request"})

#if __name__ == '__main__':
#    app.run(host='0.0.0.0',port=5000)
```

### 功能函数

实现注册、发邮件

```shell
cat main.sh
#!/bin/bash
cp -a sendemail.py sendemail_tmp.py
vpnuser=`cat emails.txt`
/usr/bin/expect openvpnCreateUser.exp $vpnuser
sed -i "s|xiongjie.liang@ihoment.com|$vpnuser|g" sendemail_tmp.py
/root/miniconda3/bin/python sendemail_tmp.py
```

### 创建VPN用户

实现创建VPN用户

```expect
cat openvpnCreateUser.exp
#!/usr/bin/expect
set email [lindex $argv 0]
spawn docker run -v /data/openvpn:/etc/openvpn --rm -it kylemanna/openvpn easyrsa build-client-full $email nopass
set timeout 60
expect {
    -timeout 20
    "*ca.key:" { send "wsq3zsQ!\r" }
   timeout { puts "expect connect timeout,pls contact ops."; return }
}
expect eof
exec sh -c "sleep 5 && docker run -v /data/openvpn:/etc/openvpn --rm kylemanna/openvpn ovpn_getclient $email > /data/openvpn/client/$email.ovpn"
exit
```

### 发送邮件

```python
cat sendemail.py

#!/usr/bin/python3
import smtplib
from email.mime.text import MIMEText
from email.mime.multipart import MIMEMultipart
from email.header import Header

# 第三方 SMTP 服务
mail_host = "smtp.mxhichina.com"  # 设置服务器
mail_user = "developer@ihoment.com"  # 用户名
mail_pass = "Jujiashike123"  # 口令

sender = 'developer@ihoment.com'
receivers = ['xiongjie.liang@ihoment.com']  # 接收邮件，可设置为你的QQ邮箱或者其他邮箱

message = MIMEMultipart()
message['From'] = Header("VPN服务器", 'utf-8') #发件名称
message['To'] =  Header("VPN客户端证书", 'utf-8') #收件人名称

subject = 'VPN证书'
message['Subject'] = Header(subject, 'utf-8')

#邮件正文内容
message.attach(MIMEText('您好，这是你的VPN客户端证书，如附件，所示', 'plain', 'utf-8'))

FILE_PATH="/data/openvpn/client/xiongjie.liang@ihoment.com.ovpn"
# 构造附件1，传送当前目录下的 test.txt 文件
att1 = MIMEText(open(FILE_PATH, 'rb').read(), 'base64', 'utf-8')
att1["Content-Type"] = 'application/octet-stream'
# 这里的filename可以任意写，写什么名字，邮件中显示什么名字
att1["Content-Disposition"] = 'attachment; filename="xiongjie.liang@ihoment.com.ovpn"'
message.attach(att1)
# try:
smtpObj = smtplib.SMTP()
smtpObj.connect(mail_host, 80)  # 25 为 SMTP 端口号
smtpObj.login(mail_user, mail_pass)
smtpObj.sendmail(sender, receivers, message.as_string())
```

### 优化后代码

```Python
# !/usr/bin/python3
# -*- coding:utf-8 -*-
import os
from flask import Flask, request, jsonify
import json,requests
import smtplib
from email.mime.text import MIMEText
from email.mime.multipart import MIMEMultipart
from email.header import Header

app = Flask(__name__)

dingding_token ='9c93902a071c5de3df0a4a4824fc87616528ac279a86ecbafa65177a994654901'
mail_user = "developer@ihoment.com"  # 用户名
mail_pass = "Jujiashike1231"  # 口令

#淡绿色字体，并@对应的人
def send_green(email_address):
    url = f"https://oapi.dingtalk.com/robot/send?access_token={dingding_token}"
    headers = {
        'Content-Type': 'application/json',
    }
    data = {
            "msgtype": "markdown",
            "markdown": {
                "title": f"VPN User",
                "text": f"<font face='黑体' color='#00EC00'>{email_address} Create test vpn Success</font>"
            }
        }
    response = requests.post(url, headers=headers, data=json.dumps(data))
    print(response.text)

def send_email(email_addr):
    # 第三方 SMTP 服务
    mail_host = "smtp.mxhichina.com"  # 设置服务器
    sender = mail_user #发送邮件
    receivers = [email_addr]  # 接收邮件，可设置���你的QQ邮箱或者其他邮箱

    message = MIMEMultipart()
    message['From'] = Header(mail_user, 'utf-8')  # 发件名称
    message['To'] = Header(mail_user, 'utf-8')  # 收件人名称

    subject = 'VPN证书'
    message['Subject'] = Header(subject, 'utf-8')
    FILE_PATH = f"/data/openvpn/client/{email_addr}.ovpn"
    print("debug email*******:",FILE_PATH)
    # 邮件正文内容
    message.attach(MIMEText('您好，这是你的VPN客户端证书，如附件，所示', 'plain', 'utf-8'))

    # 构造附件1，传送当前目录下的 test.txt 文件
    att1 = MIMEText(open(FILE_PATH, 'rb').read(), 'base64', 'utf-8')
    att1["Content-Type"] = 'application/octet-stream'
    # 这里的filename可以任意写，写什么名字，邮件中显示什么名��
    att1["Content-Disposition"] = 'attachment; filename="ihoment.com.ovpn"'
    message.attach(att1)
    # try:
    smtpObj = smtplib.SMTP()
    smtpObj.connect(mail_host, 80)  # 25 为 SMTP 端口号
    smtpObj.login(mail_user, mail_pass)
    smtpObj.sendmail(sender, receivers, message.as_string())

def check_email_url(email_address):
    # check '@'
    at_count = 0
    for element in email_address:
        if element == '@':
            at_count = at_count + 1

    if at_count != 1:
        return 0

    # check ' '
    for element in email_address:
        if element == ' ':
            return 0

    # check 'ihoment.com' or 'govee.com'
    if not (email_address.endswith("ihoment.com") or email_address.endswith("govee.com")):
        return 0

    # check char
    for element in email_address:
        if element.isalpha() == False and element.isdigit() == False:
            if element != '.' and element != '@' and element != '_':
                return 0

    return 1

@app.route("/ping", methods=['get'])
def ping_demo():
    return jsonify({"message": "", "status": 200})

@app.route('/vpn/createuser', methods=['POST'])
def creat_vpn_user():
    print("enter...")
    if request.method == 'POST':
        if not request.data:  # 检测是否有数据
            return jsonify({"error": "data params must need"})
        ret =json.loads(request.data)
        sum=check_email_url(ret["email_addr"])
        #with  open("./emails.txt","w") as f:
        #    f.write(ret["email_addr"])
        if sum == 1:
            vpnuser=ret["email_addr"]
            cmd = f"/usr/bin/expect openvpnCreateUser.exp {vpnuser}"
            #cmd="sh main.sh"
            os.system(cmd)
            send_email(vpnuser)
            send_green(vpnuser)
            return jsonify({"info msg":"Please check the email"})
        return jsonify({"error msg": "The information entered in the mailbox is incorrect. Please check it"})

    else:
        return jsonify({"error msg": "Only support POST request"})

# if __name__ == '__main__':
#    app.run(host='0.0.0.0',port=5000)
```

### 创建账号

实现创建功能，邮箱放阿里云发送信息，防止超时异常

```python
# !/usr/bin/python3
# -*- coding:utf-8 -*-
import os
from flask import Flask, request, jsonify
import json
import oss2

ali_ak="LTAI5tJJKF1icXiox9jrf6vF1"
ali_sk="Mskd1o9bqSEttgAjBZg3D20Tml1dej1"
oss_endpoint="oss-cn-shenzhen.aliyuncs.com"
oss_bucket="deploy-pro-fe"

app = Flask(__name__)

def check_email_url(email_address):
    # check '@'
    at_count = 0
    for element in email_address:
        if element == '@':
            at_count = at_count + 1

    if at_count != 1:
        return 0

    # check ' '
    for element in email_address:
        if element == ' ':
            return 0

    # check 'ihoment.com' or 'govee.com'
    if not (email_address.endswith("ihoment.com") or email_address.endswith("govee.com")):
        return 0

    # check char
    for element in email_address:
        if element.isalpha() == False and element.isdigit() == False:
            if element != '.' and element != '@' and element != '_':
                return 0

    return 1

def putobject(email_addr):
    # 阿里云账号AccessKey拥有所有API的访问权限，风险很高。强烈建议您创建并使用RAM账号进行API访问或日常运维，请登录RAM控制台创建RAM账号。
    auth = oss2.Auth(ali_ak, ali_sk)
    # Endpoint以杭州为例，其它Region请按实际情况填写。
    # 填写Bucket名称，例如examplebucket。
    bucket = oss2.Bucket(auth, f'http://{oss_endpoint}', f'{oss_bucket}')
    # 填写Object完整路径，完整路径中不包含Bucket名称，例如testfolder/exampleobject.txt。
    # 上传本地文件到oss
    bucket.put_object_from_file(f"VpnCertificate/{email_addr}.ovpn",f"/data/openvpn/client/{email_addr}.ovpn")

@app.route("/ping", methods=['get'])
def ping_demo():
    return jsonify({"message": "", "status": 200})

@app.route('/vpn/createuser', methods=['POST'])
def creat_vpn_user():
    print("enter...")
    if request.method == 'POST':
        if not request.data:  # 检测是否有数据
            return jsonify({"error": "data params must need"})
        ret =json.loads(request.data)
        sum=check_email_url(ret["email_addr"])
        if sum == 1:
            vpnuser=ret["email_addr"]
            cmd = f"/usr/bin/expect openvpnCreateUser.exp {vpnuser}"
            os.system(cmd)
            putobject(vpnuser)
            return jsonify({"info msg": "registered vpn Success"})
        return jsonify({"error msg": "The information entered in the mailbox is incorrect. Please check it"})
    else:
        return jsonify({"error msg": "Only support POST request"})

# if __name__ == '__main__':
#    app.run(host='0.0.0.0',port=5000)
```

### 启动

    gunicorn -w 2 -b 0.0.0.0:80 app:app -D

### 验证

    POST:http://cc.igovee.com/vpn/createuser
    Body:raw:{"email_addr": "xiongjie.liang@ihoment.com"}

