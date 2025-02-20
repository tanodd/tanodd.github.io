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
## Generate Random Password
```
openssl rand -base64 10
```
## User Manager
### Add User 
```
useradd name_user
adduser name_user #detail about user
```
### Switch User
```
su username
```
### Delete
```
deluser username
```
## Group Manager
```
groupadd
delgroup
usermod -aG group_name username #Append List all Group
#If not flag G, username will be delete on other group
```
### Check which group user belong to
```
group username
```
### Delete user from group
```
deluser username group
```
## Privileges
```
tan@ubuntu:~/mkdocs/tanodd.github.io$ ls -l
total 16
drwxrwxrwx 4 root root 4096 Jan  3 06:37 docs
-rwxrwxrwx 1 root root  971 Jan  3 06:38 mkdocs.yml
drwxrwxrwx 4 root root 4096 Jan  3 04:35 site
drwxrwxrwx 4 root root 4096 Dec 31 07:28 venv
```
> First Root is owner, Second Root is group owner
## Change owner
```
chown root:tan docs/
        |   |
      user group
```
cat, less, head, tail 
