# rssforever
## 简介
本项目为 Nginx + TTRSS / FreshRSS + RSSHub + Watchtower + ACME 整合 docker 容器化快速一键部署方案,支持一键脚本快速安装部署.
> *一键安装脚本已同时支持 X86 和 ARM 架构*

### 前言
[rssforever.com](https://rssforever.com) 为网友提供免费的 RSS 和 RSSHub 服务,由于个人精力,服务器压力等因素,无法保证服务的稳定性,所以尽我所能开启这个项目来为新手用户提供一键部署技术支持,特将 Nginx + TTRSS / FreshRSS + RSSHub + Watchtower + ACME 整合到 docker compose 中,并编写脚本,实现一键部署使用.

### 特点
1. 本项目针对新手用户,提供整合配置,无需繁琐的设置,即使是新手用户最快也只需要几步操作,几分钟即可部署使用.
2. 使用 docker compose 编排配置,所有命令,配置及环境变量集中管理,方便维护和迁移.
3. 更换服务器也仅需打包备份一个文件夹,迁移解压后一条命令即可恢复使用.

**一键安装脚本支持以下八种模式,请根据自身情况选择:**
1. Nginx + TTRSS + RSSHub + Watchtower + ACME 自动申请和续签证书并开启 HTTPS 模式
2. Nginx + TTRSS + RSSHub + Watchtower + 无证书 HTTP 模式
3. Nginx + TTRSS + ACME 自动申请和续签证书并开启 HTTPS 模式
4. Nginx + TTRSS + 无证书 HTTP 模式
5. Nginx + FreshRSS + RSSHub + Watchtower + ACME 自动申请和续签证书并开启 HTTPS 模式
6. Nginx + FreshRSS + RSSHub + Watchtower + 无证书 HTTP 模式
7. Nginx + FreshRSS + ACME 自动申请和续签证书并开启 HTTPS 模式
8. Nginx + FreshRSS + 无证书 HTTP 模式

### 环境需求
- 境外 VPS 服务器 ( 国内服务器网络不佳,可能导致无法下载脚本, clone 仓库, docker 拉取等问题 )
- 拥有自己的域名 ( 托管与 腾讯云 / 阿里云 / Cloudflare 方便申请证书 )
- 服务器未占用 80/443 端口
- 服务器已安装 docker 和 docker compose 环境 ( 未安装可参考下文简易安装指南 )

> 本项目不支持已被其他服务占用 80/443 端口的服务器.请停止相关服务或更换新服务器部署使用.  
> 此项目最多一共会启动 10 个容器,建议 2C2G 及以上配置.  
> 如果服务器上已有 nginx 等占用 80/443 端口的服务,同时又有部署的需求,请联系我进行付费技术支持.


## 安装
### 更新
- **2024-04-18** 更新至最新版本.
- **2022-01-06** 更新脚本支持 FreshRSS, 老版本已转移至`ttrss-rsshub`分支,同样也可以继续使用.
- **2021-07-01** 更新一键安装脚本同时支持 X86 和 ARM 架构.
- **2021-06-18** 更新一键安装脚本.

### 前期准备
- 准备 RSS 和 RSSHub 域名并解析至服务器
- 参考 [Wiki 页面](https://github.com/stilleshan/rssforever/wiki/dnsapi) 获取域名 DNSAPI 以便脚本申请证书

### 执行脚本
```shell
wget https://raw.githubusercontent.com/torinkan/rssforever/main/install.sh && chmod +x install.sh && ./install.sh
```

## 使用
### TTRSS
默认账户: admin  
默认密码: password

### FreshRSS
FreshRSS 首次访问需要设置数据库类型,选择`PostgreSQL`:
- 数据库类型：`PostgreSQL`
- 主机：`db`（不要随意修改，这里的主机名 db 已在 docker-compose.yml 中定义为服务名）
- 数据库用户名：`freshrss`（不要随意修改，这里的用户名已在 docker-compose.yml 中定义）
- 数据库密码：脚本随机生成在`rssforever`目录下的`.env`中`POSTGRES_PASSWORD`变量的值`rssforever.com-xxxxx`为数据库密码
- 数据库：`freshrss`（不要随意修改，这里的数据库名已在 docker-compose.yml 中定义）
- 表前缀：可留空或填写`freshrss_`

![snapshot01.jpg](images/snapshot01.jpg)

### 更新镜像
本项目配置有`Watchtower`来监控部分容器的镜像更新.  
Nginx / TTRSS / FreshRSS 的版本在`.env`文件中定义,请谨慎修改更新.  
如需更新,建议先行备份`rssforever`目录,再执行`docker-compose down`停止服务,修改版本号后再次执行`docker-compose up -d`启动服务.

### 更新证书
证书每月`1`日自动更新,请执行以下命令来定时每月重启`nginx`服务刷新证书.也可每月手动执行`docker-compose restart`来重启服务.
```shell
crontab -e
# 添加以下计划任务
0 0 2 * * docker restart rssforever-nginx-1
# 为避免时区问题,将在每月 2 号 0 点执行
```

### 备份恢复
#### 备份
本项目采用 docker compose 部署,所有配置及数据都在`rssforever`目录中,方便备份和迁移.
**rssforever 目录下的文件如不清楚请不要随意修改和删除,否则会导致服务无法启动.**
```shell
cd rssforever
# 进入目录
docker-compose down
# 停止所有服务
# 手动将整个 rssforever 目录迁移至新服务
```
#### 恢复
将域名重新指向新服务器,将备份的`rssforever`目录解压进入启动即可.
```shell
cd rssforever
# 进入目录
docker-compose up -d
# 启动
```

## 其他
### 感谢
感谢以下大神提供的项目:
- [Awesome TTRSS 官方文档](https://ttrss.henry.wang/)
- [Awesome TTRSS GitHub](https://github.com/HenryQW/Awesome-TTRSS)
- [RSSHub 官方文档](https://docs.rsshub.app/)
- [DIYgod/RSSHub GitHub](https://github.com/DIYgod/RSSHub)

### 链接
- [rssforever.com](https://rssforever.com)  
- [RSSHub 公共服务](https://rsshub.rssforever.com)  
- [泛域名证书申请相关文章](https://www.ioiox.com/tag/SSL/)
- [新手教程 Nginx + TTRSS + RSSHub 整合 docker 容器化快速一键部署方案](https://www.ioiox.com/archives/133.html)
