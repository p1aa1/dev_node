# 安装

## pull网络代理

/usr/lib/systemd/system/docker.service.d/http-proxy.conf

```
[Service]
Environment="HTTP_PROXY=http://192.168.2.254:1008"
Environment="HTTPS_PROXY=http://192.168.2.254:1008"

```
