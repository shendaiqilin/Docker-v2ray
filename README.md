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

## Docker run 使用方法
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
