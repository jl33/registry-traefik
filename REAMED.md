# 建立自我管理registry server
## 註冊網域
可選擇:
- Namecheap
- GoDaddy
- Squarespace(源自於Google Domain, 賣掉了)
- 在地的

並且放在DNS管理平台, 可選擇:
- Cloudflare
- ...

## 使用資源
容器:
- traefik: 反向代理伺服器, 快取/加速, load balance
- registry: registry服務(無界面)
- registry-ui: registry ui界面

服務:
- Let's Encrypt

## 步驟
- 註冊網域及DNS
- 建立acme.json
``` bash
touch acme.json
chmod 600 acme.json
```
- 將`traefik.http.routers.registry-http.rule`的Host設定進DNS管理平台的A Record中
- 建立registry的帳密
```bash
mkdir auth
docker container run --entrypoint htpasswd httpd:2 -Bbn {userid} {your-password} > auth/htpasswd
```
- 建立registry-ui的設定檔
```bash
touch config.yml
# 寫入相關設定內容
```
- 執行docker-compose.yml。完成時可以查看acme.json內容, 已包含憑證金鑰
- 以帳密登入registry server
```bash
docker login https://{traefik.http.routers.registry-http.rule指定的Host}
Username: {userid}
Password: ...
```
- 此時可以建立`traefik.http.routers.registry-http.rule`指定Host所屬的image, 並`psuh`上去自己的registry server
- 可以查看確認是否正常回傳image清單
```bash
curl GET https://{traefik.http.routers.registry-http.rule指定的Host}/v2/_catalog
```

## 參考資源
- https://cloudcasanova.com/running-docker-in-docker-on-windows/
