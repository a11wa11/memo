# Linux Command Line
### ホストネーム
```
# ホスト名を確認-1
cat /etc/hostname
# ホスト名を確認-2
hostnamectl status 
# ホスト名変更
hostnamectl set-hostname ホストネーム名
```

### ネットワーク
```
# ネットワーク設定を反映
systemctl restart network
```

### タイムゾーン
```
# タイムゾーンを確認-1
timedatectl status
# タイムゾーンを確認-2
cat /etc/localtime

# タイムゾーン変更
cp /etc/localtime /etc/localtime.org
ln -sf /usr/share/zoneinfo/Asia/Tokyo /etc/localtime
```
