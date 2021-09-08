感恩原作者rico辛苦付出 本人仅做备份和后续维护 caddy镜像更新支持tls1.3

使用教程请看wiki
```
mkdir v2ray-agent &&
cd v2ray-agent &&
curl https://raw.githubusercontent.com/shendaiqilin/Docker-v2ray/master/install-dev.sh -o install.sh &&
chmod +x install.sh &&
bash install.sh
```
免责声明
本程序仅供学习了解, 请于下载后 24 小时内删除, 不得用作任何商业用途, 文字、数据及图片均有所属版权, 如转载须注明来源

使用本程序必循遵守部署服务器所在地、所在国家和用户所在国家的法律法规, 程序作者不对使用者任何不当行为负责

### Docker run 使用方法
```
docker run -d --name=v2ray \
-e speedtest=6  -e api_port=2333 -e usemysql=0 -e downWithPanel=0 \
-e node_id=65 -e sspanel_url=https://xxxx.com -e key=mukey  -e MYSQLHOST=数据库ip地址  \
-e MYSQLDBNAME=demo_dbname -e MYSQLUSR=demo_user -e MYSQLPASSWD=demo_dbpassword -e MYSQLPORT=3306 \
--log-opt max-size=10m --log-opt max-file=5 \
--network=host --restart=always \
hulisang/v2ray_v3:go_pay
```
数据库对接将usermysql改为1

如果用到tls需要caddy反代的支持

### 中转写法
中转用法是在前端节点地址后面加上|outside_port=中转端口|relayserver=中转ip 例： // ws完整写法示例：
```
xxxxx.com;10550;16;ws;;path=/xxxxx|host=oxxxx.com|outside_port=中转端口|relayserver=中转ip
```
其他写法自行添加 前提：已经将中转ip通过iptables、socat、brook等方法完成中转落地

#### 作为v2ray后端使用

这里面板设置是节点类型v2ray, 普通端口。 v2ray的API接口默认是2333

支持 tcp,kcp、ws+(tls 由镜像 Caddy或者ngnix 提供,默认是443接口哦)。或者自己调整。

```
没有CDN的域名或者ip;端口（外部链接的);AlterId;协议层;;额外参数(path=/xxxxx|host=xxxx.win|inside_port=10550这个端口内部监听))

// ws 示例
xxxxx.com;10550;16;ws;;path=/xxxxx|host=oxxxx.com

// ws + tls (Caddy 提供)
xxxxx.com;0;16;tls;ws;path=/xxxxx|host=oxxxx.com|inside_port=10550
xxxxx.com;;16;tls;ws;path=/xxxxx|host=oxxxx.com|inside_port=10550



// nat🐔 ws 示例
xxxxx.com;11120;16;ws;;path=/xxxxx|host=oxxxx.com

// nat🐔 ws + tls (Caddy 提供)
xxxxx.com;0;16;tls;ws;path=/xxxxx|host=oxxxx.com|inside_port=10550|outside_port=11120
xxxxx.com;;16;tls;ws;path=/xxxxx|host=oxxxx.com|inside_port=10550|outside_port=11120
```

目前的逻辑是

* 如果为外部链接的端口是0或者不填，则默认监听本地127.0.0.1:inside_port
* 如果外部端口设定不是 0或者空，则监听 0.0.0.0:外部设定端口，此端口为所有用户的单端口，此时 inside_port 弃用。
* 默认使用 Caddy 镜像来提供 tls，控制代码不会生成 tls 相关的配置。Caddyfile 可以在Docker/Caddy_V2ray文件夹里面找到。
* Nat🐔，如果要用ws+tls，则需要使用outside_port=xxx，php后端会生成订阅时候，使用outside_port覆盖port部分。 outside_port是内部映射端口， 建议内网和外网的两个端口数值一致。 tcp 配置：

```
xxxxx.com;非0;16;tcp;;
```

kcp 支持所有 v2ray 的 type：

* none: 默认值，不进行伪装，发送的数据是没有特征的数据包。

```
xxxxx.com;非0;16;kcp;noop;
```

* srtp: 伪装成 SRTP 数据包，会被识别为视频通话数据（如 FaceTime）。

```
xxxxx.com;非0;16;kcp;srtp;
```

* utp: 伪装成 uTP 数据包，会被识别为 BT 下载数据。

```
xxxxx.com;非0;16;kcp;utp;
```

* wechat-video: 伪装成微信视频通话的数据包。

```
xxxxx.com;非0;16;kcp;wechat-video;
```

* dtls: 伪装成 DTLS 1.2 数据包。

```
xxxxx.com;非0;16;kcp;dtls;
```

* wireguard: 伪装成 WireGuard 数据包(并不是真正的 WireGuard 协议) 。

```
xxxxx.com;非0;16;kcp;wireguard;
```
### 作为SS后端使用
面板配置是节点类型为 Shadowsocks，普通端口。 后端配置与ssr后端一致 加密方式只支持：

* [X] aes-256-cfb
* [X] aes-128-cfb
* [X] chacha20
* [X] chacha20-ietf
* [X] aes-256-gcm
* [X] aes-128-gcm
* [X] chacha20-poly1305 或称 chacha20-ietf-poly1305
* [X] xchacha20-ietf-poly1305
