### Volume
#### List Volume
```
docker volume ls
```
#### Create Volume
```
docker volume create name_volume
```
####  Remove all unused local volumes
```
docker volume prune
```
#### Backup my_volume to my_backup.tar
```
docker run --rm --volume my_volume:/mount_bkp --volume $(pwd):/backup ubuntu tar cvf /backup/my_backup.tar /mount_bkp
```
#### Restore my_backup.tare to my_volume
```
docker run --rm --volume my_volume:/mount_bkp --volume $(pwd):/backup ubuntu tar xvf /backup/my_backup.tar -C /mount_bkp --strip 1
```
### Container
#### Remove all container
```
docker rm $(docker ps -a -q)
```