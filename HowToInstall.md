---
> 2022/2
> https://becoder.org/ubuntu-server-transmission-setting/
---

# 安裝流程
## 先在 PVE 開好虛擬機(VM)
- OS        : Ubuntu-20.04.3-server-amd64
- CPU       : host
- Memory    : 512 MB / 4096 MB
- Disk      : 16 G
- BIOS      : Default(SeaBIOS)
- 機器架構  : Default(i440fx)

## 使用 windows terminal 登入
- 純粹是我習慣

## 安裝 qemu-guest-agent
- 記得在 PVE 開啟相關設定
```
sudo apt install qemu-guest-agent
systemctl start qemu-guest-agent
```

## 更新相關檔案
- 單純自己習慣
```
sudo apt update
sudo apt upgrade
```

## 安裝 Transmission
- -daemon 是專門用來提供 webGUI 登入
    - 不加任何 `-XX` 會一次大補丸通通裝，不過因為我只需要 webGUI 所以特別指定 -daemon
```
apt install transmission-daemon
```

## 安裝 NFS
```
apt install nfs-common
```

## 設定檔中做了一些操作
- `/etc/transmission-daemon/settings.json`
    - debian-transmission:debian-transmission
```
"download-dir": ""                      # 儲存位置，雖然我是沒動
"rpc-authentication-required": false,   # 網頁需要認證，直接關了，下面那些帳號密碼就不用管了
"rpc-whitelist-enabled": false          # 可登入 IP 白名單，直接關了
```

## 重新啟動 transmission-daemon
```
service transmission-daemon reload

# 用 restart 會將設定重置
```

## TrueNAS 做好準備
    1. 準備一個帳戶
    - uid : 113
    - gid : 117
    - 開啟 NFS 共用
    
## 掛載 NAS 提供的空間
```
sudo mount -o bg,soft,rsize=32768,wsize=32768 192.168.0.100:/mnt/OU_House/BT_storage /var/lib/transmission-daemon/downloads
```

## 開心使用

# 最佳化
## `/etc/transmission-daemon/settings.json`
```
"download-queue-size": 10       # 預設 5，同時下載限制
"cache-size-mb": 256            # 預設 4，快取大小
"seed-queue-enabled": true      # 預設 false，最大做種數，跟下面的 size 搭配
```

## webGUI
- 上傳速度 : 315 kB/s
- 連接點數 (peer)
    - Max peers per torrent : 100
    - Max peers overall     : 800
