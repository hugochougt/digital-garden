# Ubuntu 服务器配置

新购服务器后对服务器进行必要的配置和安全加固。

## 1. 更新系统和安装常用软件包

```bash
apt-get update && apt-get upgrade -y
apt-get install git vim htop curl wget locales net-tools unattended-upgrades software-properties-common -y
```

## 2. 主机配置

一般云主机厂商生成的 hostname 都是没意义的字符串，改成方便自己识别的 hostname。

```bash
ssh root@server-ip
sudo hostnamectl set-hostname new-name
# 配置 locale 为 en_US.UTF-8
export LANGUAGE=en_US.UTF-8
export LANG=en_US.UTF-8
export LC_ALL=en_US.UTF-8
sudo locale-gen en_US.UTF-8
sudo dpkg-reconfigure locales
reboot now
```

## 3. 新增日常维护用户

```bash
read -p "Enter your new username: " USER_NAME
PASSWORD=$(openssl rand -base64 32 | cut -c 1-32)
# Add new user
useradd -s /bin/bash -d /home/$USER_NAME -p "${PASSWORD}" -m $USER_NAME
# s 指定用户的 login shell
# d 指定用户 home 目录
# p 指定用户密码
# m 创建用户的 home 目录
usermod -aG sudo ${USER_NAME} # 添加用户到 sudo group
echo "Your password of ${USER_NAME} is ${PASSWORD}"
```

新用户 sudo 时不需要输入密码：

1. 输入 `visudo`
2. 找到 `root ALL=(ALL:ALL) ALL` 这行，添加下方内容
3. `username ALL=(ALL) NOPASSWD: ALL`，username 为上一步的 `USER_NAME` 的值

新开一个命令行窗口，检查新用户是否能登录成功。

```bash
ssh newuser@server-ip
# 设置用户 .ssh 目录
read -p "Enter your local PC ssh pub key: " SSH_PUB_KEY
mkdir ~/.ssh
chmod 700 ~/.ssh
ssh-keygen -t rsa -b 2048 -N "" -f ~/.ssh/id_rsa
echo "${SSH_PUB_KEY}" > ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
```

新开一个命令行窗口，检查新用户是否能不输入登录。

## 4. SSH 安全配置

备份并编辑服务器上的 `/etc/ssh/sshd_config` 文件：

```bash
sudo cp /etc/ssh/sshd_config ./sshd_config.$(date +%s).bak
sudo vim /etc/ssh/sshd_config
```

修改以下配置到对应的值：

- Port 22 -> `Port 22xxx`: 改成从 1025 到 65536 之间的任意一个整数
- PermitRootLogin -> `PermitRootLogin no`
- PermitEmptyPassword -> `PermitEmptyPasswords no`
- PasswordAuthentication -> `PasswordAuthentication no`
- RSAAuthentication -> `RSAAuthentication yes`
- PubkeyAuthentication -> `PubkeyAuthentication yes`
- AuthorizedKeysFile -> `AuthorizedKeysFile .ssh/authorized_keys`
- UseDNS -> `UseDNS no`

允许新用户 SSH 登录

```bash
sudo echo "AllowUsers ${USER_NAME}" >> /etc/ssh/sshd_config
```

重启 SSHD：`sudo service sshd restart`

在本机的 `~/.ssh/config` 添加新的 ssh 配置：

```plain
Host alias
HostName server-ip
User username
Port new-port
```

新开一个命令行窗口，检查是否能通过 `ssh alias` 登录。

## 5. 启用防火墙

```bash
sudo apt install ufw
sudo ufw allow 22xxx # 上一步修改的 ssh 端口
sudo ufw allow http
sudo ufw allow https
sudo ufw show added
sudo ufw enable
sudo ufw status verbose
sudo ufw disable # 停用防火墙
```

## 6. Fail2ban

```bash
sudo apt install fail2ban -y
sudo service fail2ban restart
```

## 7. 常用 bash alias

```bash
alias ..='cd ..'
alias ...='cd ../..'
alias ip='curl ipinfo.io/ip && echo ""'
alias ls='ls --color=auto -A -l -r -t -h'
alias l='ls --color=auto -A -l -r -t -h'
alias md='mkdir -pv'
alias ports='netstat -tulanp'
alias reboot='sudo reboot'
# do not delete / or prompt if deleting more than 3 files at a time
alias rm='rm -I --preserve-root'
alias update='sudo apt update && sudo apt upgrade -y'
alias vi=vim
```

把上述内容保存到 `~/.bash_aliases`，并且执行 `. ~/.bash_aliases` 命令。

可以检查 `.bashrc` 文件，是否有以下内容：

```bash
if [ -f ~/.bash_aliases ]; then
  . ~/.bash_aliases
fi
```

## 8. 基本 vim 配置

```bash
curl -o ~/.vimrc https://raw.githubusercontent.com/amix/vimrc/master/vimrcs/basic.vim
```

## 参考

- [Hardening Ubuntu. Systemd edition.](https://github.com/konstruktoid/hardening)
- [Linux服务器的初步配置流程](https://www.ruanyifeng.com/blog/2014/03/server_setup.html)
- [HN: Securing a Linux Server](https://news.ycombinator.com/item?id=6384603)
- [My First 10 Minutes On a Server - Primer for Securing Ubuntu](https://blog.codelitt.com/my-first-10-minutes-on-a-server-primer-for-securing-ubuntu/)
