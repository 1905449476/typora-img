# <u>AWS部署wordpress应用</u>

## 创建网络部分

### vpc

1.首先创建vpc，名称为intnet1，CIDR为：172.16.0.0/16![image-20220407104427994](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220407104427994.png)

### 子网

在VPC intnet1中创建子网（2个共有子网和4个私有子网）

| 名称        |        CIDR        | 可用区 |
| ----------- | :----------------: | ------ |
| **Public1** | **172.16.1.0/24**  | 1a     |
| **Public2** | **172.16.2.0/24**  | 1b     |
| **Web1**    | **172.16.3.0/24**  | 1a     |
| **Web2**    | **172.16.4.0/24**  | 1b     |
| **Db1**     | **172.16.13.0/24** | 1a     |
| **Db2**     | **172.16.14.0/24** | 1b     |

![image-20220409100604690](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220409100604690.png)

### 互联网网关

创建互联网网关，名称为intnet-IGW，并且关联intnet VPC

![image-20220407111453000](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220407111453000.png)

![image-20220409101153090](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220409101153090.png)

![image-20220409101136993](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220409101136993.png)

### 路由表

将已经创建的主路由表改名为IGW

![image-20220409101038112](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220409101038112.png)

创建路由表，并将已创建的子网进行关联--

NAT1

![image-20220409100503061](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220409100503061.png)

NAT2

![image-20220409100903209](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220409100903209.png)

DB

![image-20220409100923076](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220409100923076.png)

### NAT网关

创建NAT网关（分配弹性IP）

NGW1

![image-20220409101356813](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220409101356813.png)

NGW2

![image-20220409101433835](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220409101433835.png)

给已经创建的弹性IP打上标签

![image-20220409102957849](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220409102957849.png)

### 添加路由和子网关联

NAT1路由表添加NGW1路由

![image-20220409101641293](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220409101641293.png)

将NAT1路由表与web1子网做关联

![image-20220409101755166](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220409101755166.png)

NAT2路由表同上，NAT2路由表添加NGW2路由

![image-20220409101920635](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220409101920635.png)

将NAT2路由表与web2子网做关联

![image-20220409101935865](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220409101935865.png)

将IGW路由表关联public1子网和public2子网，并且添加intnet-IGW互联网网关

![image-20220409102101670](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220409102101670.png)

![image-20220409102133867](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220409102133867.png)

db路由表关联db1子网和db2子网

![image-20220409102233467](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220409102233467.png)

### 安全组

创建web和db安全组

web安全组

![image-20220409104510607](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220409104510607.png)

db安全组

![image-20220409104834826](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220409104834826.png)

## 创建数据库

### 创建数据库子网组

名称为db1，vpc选择intnet1，添加db1和db2的子网

![image-20220409103218065](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220409103218065.png)

![image-20220409103225613](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220409103225613.png)

![image-20220409103300032](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220409103300032.png)

### 创建数据库

引擎类型选择：mysql

版本：mysql 5.7.22

模板：免费套餐

数据库实例标识符：db1

主用户名：wordpress

密码：123123123

数据库类型：t2.micro

vpc：intnet1

子网组：db1

公开访问：否

vpc安全组：db

可用区：1a

数据库名称：wordpress

备份：启用数据库备份



![image-20220409103424483](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220409103424483.png)

![image-20220409103534075](C:/Users/%E5%97%A8%E5%AE%A2/AppData/Roaming/Typora/typora-user-images/image-20220409103534075.png)

![image-20220409105033932](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220409105033932.png)



![image-20220409105404152](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220409105404152.png)

## 创建EC2实例

### web3

在web1中创建wordpress实例

![image-20220409105739828](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220409105739828.png)

并写入用户数据

```bash
#!/bin/bash -ex
amazon-linux-extras install -y php7.4
yum -y install httpd
sed -i  's/Listen 80/Listen 8888/' /etc/httpd/conf/httpd.conf
wget -P /var/www/html/ https://cn.wordpress.org/latest-zh_CN.zip
unzip /var/www/html/latest-zh_CN.zip -d /var/www/html/
cp -r /var/www/html/wordpress/* /var/www/html/
cd /var/www/
chown -R apache /var/www
chgrp -R apache /var/www
chmod 2775 /var/www
systemctl start httpd
systemctl enable httpd
```

![image-20220409105757927](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220409105757927.png)

![image-20220409205723421](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220409205723421.png)

![image-20220410104159239](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220410104159239.png)

![image-20220410104233233](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220410104233233.png)

### web2

在web2中创建wordpress实例

![image-20220410104507624](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220410104507624.png)

![image-20220409105757927](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220409105757927.png)

![image-20220410104601671](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220410104601671.png)

![image-20220410104623799](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220410104623799.png)

![image-20220410104642515](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220410104642515.png)

## 创建负载均衡器

### 目标群组

创建目标群组，类型选择为：实例

名称为：web-gfgroup

放行端口为：8888

vpc：intnet1

![目标群组](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220410172057939.png)

![名称端口vpc](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220410191723034.png)

![阈值合作](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220410192238810.png)

注册目标选择已经创建好的web1和web2实例，注册添加

![注册目标](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220410192330201.png)

创建目标组

![注册目标检查](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220410192355947.png)

创建完毕

![创建完成](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220410192416281.png)

###  ALB

创建负载均衡器，选择ALB

![负载均衡](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220410171942302.png)

名称为：web-ALB

![alb](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220410192917503.png)

![alb网络映射](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220410195512714.png)

安全组选择web安全组

![安全组](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220410195727586.png)

侦听器和路由

端口选择：8888

转发至：web-gfgroup

![端口和转发侦听器](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220410195853456.png)

创建完毕

![web-alb](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220410195938873.png)

等待一段时间等预置完毕，变为活跃，访问DNS名称地址加上端口8888

```html
web-alb-1227226383.cn-northwest-1.elb.amazonaws.com.cn:8888
```

![DNS域名访问测试](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220410201822313.png)

## 创建AMI映像

依照EC2中web1实例创建一个名为wordpress-image的AMI

选中web1实例，右键选择映像和模板，选择创建映像

![创建映像](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220410202202403.png)

名称为wordpress-image，标签为Name为wordpress-image

![映像名称](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220410202248637.png)

![映像创建完毕](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220410202411183.png)

## 创建Auto scaling组

### 创建启动配置

启动配置名称为：template-web

AMI选择为：wordpress-image

实例类型：t2.micro

![启动配置](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220410202919407.png)

启动 CloudWatch 中的 EC2 实例详细监控

![cloudwatch监控](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220410202944967.png)

写入一个用户数据，避免web服务启动不来，保险起见

```bash
systemctl restart httpd
```

![用户数据](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220410202952119.png)

安全组：选择现有的安全组web安全组

![安全组](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220410203107442.png)

密钥对使用：hdl-1

![密钥对](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220410203159392.png)

创建启动配置

![创建启动配置](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220410203224665.png)

### 创建Auto Scaling组

![创建as组](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220410203309587.png)

名称为：web-asgroup

启动配置选择为：template-web

![image-20220410203400891](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220410203400891.png)

网络

VPC选择：intnet1

子网选择：web1和web2

![network](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220410203456900.png)

负载均衡选择：附加到现有的负载均衡器

选择目标组为：web-gfgroup

![添加负载均衡](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220410203603261.png)

运行状态检查选择：ELB

开启cloudwatch监控收集

![ELB和cloudwatch](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220410203714532.png)

组大小：最小容量为1，最大容量为4

![组大小](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220410203912258.png)

标签：Name=web-asgroup

![image-20220410204046720](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220410204046720.png)

创建完毕

![创建完毕as组](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220410204124598.png)

## 添加Cloud Watch告警策略

切换到cloud watch控制台

![cloud watch告警设置](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220410204315392.png)

![image-20220410204939147](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220410204939147.png)

![image-20220410204954376](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220410204954376.png)

EC2中选择按Auto Sacling组，选择web-asgroup的CPUUtilization（CPU利用率）

![image-20220410205309058](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220410205309058.png)

CPU利用率在5分钟内的最大值小于40%，且连续发生3次。则实例数减少1台。冷却60秒

![image-20220410205606259](https://cdn.jsdelivr.net/gh/1905449476/typora-img/aws/image-20220410205606259.png)











