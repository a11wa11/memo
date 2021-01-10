#!/bin/bash
# 1. first setup
yum update -y
localectl set-locale LANG=ja_JP.utf8
timedatectl set-timezone Asia/Tokyo
yum install -y tree vim wget openssl-devel epel-release
yum groupinstall -y "Development Tools"
cd /root
# 2. python setup
wget https://www.python.org/ftp/python/3.5.9/Python-3.5.9.tar.xz
tar -Jxvf Python-3.5.9.tar.xz
Python-3.5.9/configure --prefix=/usr/local/python
make
make install
rm -rf /root/*
ln -s /usr/local/python/bin/python3.5 /usr/local/bin/python
ln -s /usr/local/python/bin/pip3.5 /usr/local/bin/pip
echo "export PATH=$PATH:/usr/local/python/bin" >> ~/.bashrc
# 3. desktop setup
yum install -y xrdp tigervnc-server
yum groupinstall -y "GNOME Desktop"
cp /etc/ssh/sshd_config /etc/ssh/sshd_config.bk
cp /etc/xrdp/xrdp.ini /etc/xrdp/xrdp.ini.bk
sed -i s/enabled=1/enabled=0/g /etc/yum.repos.d/epel.repo
sed -i s/"PasswordAuthentication no"/"PasswordAuthentication yes"/ /etc/ssh/sshd_config
sed -i s/max_bpp=32/max_bpp=16/ /etc/xrdp/xrdp.ini
systemctl start firewalld
firewall-cmd --permanent --zone=public --add-port=3389/tcp
firewall-cmd --permanent --zone=public --add-port=5000/tcp
firewall-cmd --reload
systemctl set-default multi-user.target
systemctl stop packagekit
systemctl mask packagekit
systemctl start xrdp
systemctl enable xrdp
# 4. mysql setup
yum install -y mysql
# 5. nginx setup
touch /etc/yum.repos.d/nginx.repo
echo '[nginx-stable]' >> /etc/yum.repos.d/nginx.repo
echo 'name=nginx stable repo' >> /etc/yum.repos.d/nginx.repo
echo 'baseurl=http://nginx.org/packages/centos/$releasever/$basearch/' >> /etc/yum.repos.d/nginx.repo
echo 'gpgcheck=1' >> /etc/yum.repos.d/nginx.repo
echo 'enabled=1' >> /etc/yum.repos.d/nginx.repo
echo 'gpgkey=https://nginx.org/keys/nginx_signing.key' >> /etc/yum.repos.d/nginx.repo
echo 'module_hotfixes=true' >> /etc/yum.repos.d/nginx.repo
yum install -y nginx