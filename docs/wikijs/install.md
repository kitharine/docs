# Wikijs 安装记录
[wiki官网](https://js.wiki/)
[官方安装教程](https://docs.requarks.io/en/install/linux)
[硬件要求](https://docs.requarks.io/install/requirements)
## 宿主机 Debain11.x
 Debain 换源
 `/etc/apt/sources.list`
```bash
  # 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye main contrib non-free
# deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye main contrib non-free

deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye-updates main contrib non-free
# deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye-updates main contrib non-free

deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye-backports main contrib non-free
# deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye-backports main contrib non-free

deb https://mirrors.tuna.tsinghua.edu.cn/debian-security bullseye-security main contrib non-free
# deb-src https://mirrors.tuna.tsinghua.edu.cn/debian-security bullseye-security main contrib non-free

# deb https://security.debian.org/debian-security bullseye-security main contrib non-free
# # deb-src https://security.debian.org/debian-security bullseye-security main contrib non-free
```
  
## 软件
[数据库pgsql](https://www.postgresql.org/download/linux/debian/)
[nodejs](/home)

### 1. nodejs
换源后操作
```bash
apt update
apt install nodejs
```
### 2. postgresql
```bash
## Create the file repository configuration:
sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
# Import the repository signing key:
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | apt-key add -

# Update the package lists:
apt-get update

# Install the latest version of PostgreSQL.
# If you want a specific version, use 'postgresql-12' or similar instead of 'postgresql':
apt-get -y install postgresql
```

#### 2.1 配置pgsql
##### 2.1.1 查找pg_hba.conf
```bash
su postgres
psql -U postgres
select setting from pg_settings where name like '%hba%';
```
##### 2.1.2 修改数据库用户默认密码
```bash
alter user postgres with password 'new_password'
```
##### 2.1.2 修改pg_hba.conf
```bash
将`peer` 改为 `trust`
```
#### 2.2 启动postgresql
```bash
pg_ctlcluster 13 main start
```

## 安装wikijs
1. 下载最新版 Wiki.js:
  ```bash
  wget https://github.com/Requarks/wiki/releases/latest/download/wiki-js.tar.gz
  ```
2. 解压:
  ```bash
  mkdir wiki
  tar xzf wiki-js.tar.gz -C ./wiki
  cd ./wiki
  ```
3. 将sample.yml 更名为 `config.yml`:
  ```bash
  mv config.sample.yml config.yml
  ```
4. 修改数据库配置:
  ```bash
  nano config.yml
  ```
  4.1. 将以下配置修改为自己的
  ```yml
  db:
    type: postgres
    host: localhost
    port: 5432
    user: wikijs
    pass: wikijsrocks
    db: wiki
  ```

5. Run Wiki.js
  ```bash
  node server
  ```
6. Wait until you are invited to open to the setup page in your browser.
7. Complete the setup wizard to finish the installation.

>How to run wiki as services
{.is-danger}
 
## 配置SSL
1. 创建/申请证书，并上传到服务器指定目录 `/wiki/SSL`
```bash
cd wiki
mk SSL
```

2. 修改config.yml
```bash
#原配置
ssl:
  enabled: true
  port: 3443

  # Provider to use, possible values: custom, letsencrypt
  provider: custom

  # ++++++ For custom only ++++++
  # Certificate format, either 'pem' or 'pfx':
  format: pem
  # Using PEM format:
  key: path/to/key.pem
  cert: path/to/cert.pem
  
```
3. 重启wiki




