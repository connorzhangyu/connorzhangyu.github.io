---
title: Grafana
categories:
  - Linux
tags:
  - 监控
cover: https://image.runtimes.cc/202404051528868.jpeg
abbrlink: 49186
date: 2022-11-19 18:25:00
updated: 2024-09-15 18:04:17
---

# 监控主机

| HostName | IP       |
| -------- | -------- |
| master   | 10.0.0.6 |
| node1    | 10.0.0.9 |

| 服务          | 端口 |
| ------------- | ---- |
| Grafana       | 3000 |
| Prometheus    | 9090 |
| node-exporter | 9100 |
| Cadvisor      | 8080 |

## 部署Prometheus(master机器)

```shell
docker run -d \
    --name=prometheus \
    --restart=always \
    -p 9090:9090 \
    -v /root/prometheus/prometheus.yml:/etc/prometheus/prometheus.yml \
    prom/prometheus
```

编辑prometheus.yml配置文件

```yaml
scrape_configs:
  - job_name: 'prometheus'
    static_configs:
    - targets: ['10.0.0.6:9090']

 	#添加master及node节点添加
  - job_name: 'master'
    static_configs:
    - targets: ['10.0.0.6:9100']
  
  - job_name: 'node'
    static_configs:
    - targets: ['10.0.0.9:9100']
```

> 这里只是提前编辑好,之后如果要添加新的机器,再后面添加

访问master的9090端口,`http://10.0.0.6:9090/`

![](https://image.runtimes.cc/202404051421127.png)

## 部署node_exporter(master和node)

看需求,master不部署也可以

{% tabs docker %}
<!-- tab docker安装-->

```shell
docker run -d \
      --name node-exporter \
      --restart=always \
      -p 9100:9100 \
      -v "/proc:/host/proc:ro" \
      -v "/sys:/host/sys:ro" \
      -v "/:/rootfs:ro" \
      prom/node-exporter
```
<!-- endtab -->

<!-- tab 源码安装-->

```bash
# 下载最新的node_exporter版本
wget https://github.com/prometheus/node_exporter/releases/download/v1.6.1/node_exporter-1.6.1.linux-amd64.tar.gz

# 解压缩下载的文件
tar xvfz node_exporter-1.6.1.linux-amd64.tar.gz

# 将可执行文件移动到 /usr/local/bin
sudo mv node_exporter-1.6.1.linux-amd64/node_exporter /usr/local/bin/

# 创建 node_exporter 用户
useradd -rs /bin/false node_exporter

# 创建 systemd 服务
vim /etc/systemd/system/node_exporter.service

[Unit]
Description=Node Exporter
Wants=network-online.target
After=network-online.target

[Service]
User=node_exporter
Group=node_exporter
Type=simple
ExecStart=/usr/local/bin/node_exporter

[Install]
WantedBy=multi-user.target

# 重新加载 systemd 配置
sudo systemctl daemon-reload

# 启动 node_exporter 服务
sudo systemctl start node_exporter

# 设置为开机自动启动
sudo systemctl enable node_exporter
```
<!-- endtab -->
{% endtabs %}


验证访问`http://<你的服务器IP>:9100/metrics`

![](https://image.runtimes.cc/202404051421323.png)

点击Mertrics,下图为node通过9100端口暴露出的监控指标

![](https://image.runtimes.cc/202404051421669.png)

再次访问master的9090端口,就会显示上面的 `UP` 状态

再修改`prometheus.yml`

```yaml
scrape_configs:
  - job_name: 'node_exporter'
    static_configs:
      - targets: ['<你的服务器IP>:9100']
```

## 部署Grafana

```bash
# 新建空文件夹grafana,用来存储数据
mkdir /root/grafana

# 设置权限
chmod 777 -R /root/grafana

# 运行
docker run -d \
      -p 3000:3000 \
      --name=grafana \
      -e "GF_SECURITY_ADMIN_PASSWORD=admin" \
      -v /root/grafana:/var/lib/grafana \
      grafana/grafana
```

访问master的3000端口 默认账号密码都为admin

创建数据源

![](https://image.runtimes.cc/202404051421951.png)


联接`Prometheus`

![](https://image.runtimes.cc/202404051421144.png)


点击Save & Test

创建面板

![](https://image.runtimes.cc/202404051421239.png)

- 点击Import
- 输入模板代码9276
- 点击Load
- 再下拉框选中`Prometheus`
- 点击Import

![](https://image.runtimes.cc/202404051421239.png)

查看,选中要查看的主机

![](https://image.runtimes.cc/202404051421087.png)

# 监控节点主机的容器

`cAdvisor`（Container Advisor）用于采集正在运行的容器资源使用和性能信息。

`cAdvisor`可以对节点机器上的资源及容器进行实时监控和性能数据采集，包括`CPU使用情况内存使用情况 网络吞吐量`及`文件系统使用情况`

## 部署Cadvisor

```bash
docker run -d \
      --volume=/:/rootfs:ro \
      --volume=/var/run:/var/run:ro \
      --volume=/sys:/sys:ro \
      --volume=/var/lib/docker/:/var/lib/docker:ro \
      --volume=/dev/disk/:/dev/disk:ro \
      --publish=8080:8080 \
      --detach=true \
      --name=cadvisor \
      google/cadvisor:latest
```

访问节点IP+端口

![](https://image.runtimes.cc/202404051422766.png)

修改prometheus.yml,在末尾添加,修改玩之后要重启`Prometheus`

```yaml
- job_name: 'docker'
    static_configs:
    - targets: ['10.0.09:8080']
```

访问节点IP+9090

![](https://image.runtimes.cc/202404051422992.png)

**然**后在master主机登录Grafana，导入Docker监模板，id：193

[在docker中搭建micrometer + grafana + prometheus的jvm监控 - 掘金](https://juejin.cn/post/6982487112624898062)

# 监控Mysql

```yaml
docker run -d \
  --name mysql_exporter \
  --restart always \
  -p 9104:9104 \
  -e DATA_SOURCE_NAME="root:123456@(10.0.0.8:3306)/" \
  prom/mysqld-exporter
```

模板ID:**7362**
