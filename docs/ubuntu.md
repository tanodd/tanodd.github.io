### Set Static IP
#### With Old version Ubuntu
```
sudo nano /etc/netplan/01-netcfg.yaml

edit content:
#######################################
network:
  version: 2
  renderer: networkd
  ethernets:
    ens3:
      dhcp4: no
      addresses:
        - 192.168.121.221/24
      gateway4: 192.168.121.1
      nameservers:
          addresses: [8.8.8.8, 1.1.1.1]
```
#### New version (>=22.04)
```
sudo apt install openvswitch-switch

network:
  version: 2
  renderer: networkd
  ethernets:
    eth0:
      dhcp4: no
      addresses:
        - 192.168.0.10/24
      routes:
        - to: 0.0.0.0/0
          via: 192.168.0.1
      nameservers:
          addresses: [8.8.8.8, 1.1.1.1]
```
### Troubleshoot DNS 
```
sudo nano /etc/resolv.conf
```

### Check what port running attach with what service
```
netstat -lntup
sudo lsof -i -P -n
```
### Check driver Linux
```
lspci -v
```
### Set timezone Vietnam
```
timedatectl set-timezone Asia/Ho_Chi Minh
```
