## Ubuntu Immigration
### Backup
```bash
sudo su
cd / # THIS CD IS IMPORTANT THE FOLLOWING LONG COMMAND IS RUN FROM /
tar -cvpzf backup.tar.gz \
--exclude=/backup.tar.gz \
--exclude=/proc \
--exclude=/tmp \
--exclude=/mnt \
--exclude=/dev \
--exclude=/sys \
--exclude=/run \
--exclude=/media \
--exclude=/var/log \
--exclude=/var/cache/apt/archives \
--exclude=/usr/src/linux-headers* \
--exclude=/home/*/.gvfs \
--exclude=/home/*/.cache \
--exclude=/home/*/.local/share/Trash /
```
```bash
tar -cvpzf backup.tar.gz --exclude=/backup.tar.gz --exclude=/proc --exclude=/tmp --exclude=/mnt --exclude=/dev --exclude=/sys --exclude=/run --exclude=/media --exclude=/var/log --exclude=/var/cache/apt/archives --exclude=/usr/src/linux-headers* --exclude=/home/*/.gvfs --exclude=/home / --warning=no-file-changed
```
`split -d -b 3900m /path/to/backup.tar.gz /name/of/backup.tar.gz.`

### Restore
```bash
lsblk
#Format the disk
sudo mkfs -t ext4 /dev/sdXY
#Mount the disk where restoring to
sudo mount /dev/sdXY /mnt
#Unzip the backup to /mnt
sudo tar -xvpzf /path/to/backup.tgz -C /mnt --numeric-owner
#Create some folders
cd /mnt && mkdir proc mnt dev sys run media 
```
`cat *.tar.gz.* | tar -xvpzf - -C /`

### Setup fstab
```bash
#Find the uuid 
sudo blkid
#Duplicate
cd /mnt/etc && sudo cp fstab fstab.$(date +%Y-%m-%d) 
#Compare 
cmp fstab fstab.$(date +%Y-%m-%d) 
#Edit /etc/fstab 
sudo nano /etc/fstab 
```
[ref1](https://www.cnblogs.com/augusite/p/10930793.html)
[ref2](https://help.ubuntu.com/community/Partitioning/Home/Moving)

### Grub
```bash
#Mount the ESP
sudo mount /dev/sda4 /mnt/boot/efi
#Mirror files
for i in /dev /dev/pts /proc /sys /run; do sudo mount -B $i /mnt/$i; done
#Root to /mnt
sudo chroot /mnt
#grub reintsall or fix
grub-install /dev/sdX
update-grub
```
[ref1](https://askubuntu.com/questions/831216/how-can-i-reinstall-grub-to-the-efi-partition)
[ref2](https://help.ubuntu.com/community/BackupYourSystem/TAR)
## windows C 盘无法打开
`CHKDSK C:/F`; windows C display abnormal without actual volume, only a letter.


## Something Incredible 
![couldn't believe that I had run the command](../docs/img/2021-01-07_19-13-17.png)

`who -a` did not give the positive message
## My ubuntu 20.04 no display
``` bash
journalctl -xe
sudo apt-get purge '^nvidia.*'
reboot
```
## Git config(Two-factor)
```bash
git config --global user.name ""
git config --global user.email ""
git clone xxxx.git
# Username for 'https://github.com':
# Password for 'github.com':   ;(Personal access tokens)
git config --global credential.help store
```
## nautilus

## delete .DS_Store and ._files
```bash
find . -type f -name '._*' -delete
find . -type f -name '.DS_Store*' -delete
```

## 无用之用

1. 了解`gitbook`；
2. 阅读`git`教程，略懂`rebase`变基；
3. `sed`常用方式；
    ```
    # delete the first 10 lines of a file
    sed '1,10d'
    
    # delete the last line of a file
    sed '$d'
    
    # 如果需要替换行内所有的abc，就需要引入命令 g：
    sed 's/abc/ABC/g' datafile
    
    # 多个命令的组合：
    sed -e '1,10d'-e 's/abc/ABC/g' datafile 
    
    # 如何在第5行到第10行每行前面加字符`>`？
    $ sed '5,10s/^/> /' test.md

    # To remove 1st n characters of every line:
    $ sed -i -r 's/.{4}//' file
    $ sed -i 's/...//' file
    A single dot(.) removes 1st character, 3 dots remove 1st three characters.
        
    # 如何将第2行置为空行？
    sed -e '1G' -e '2d' index.md
    
    # 如何将第1行置空？
    sed -e '1{x;p;x;}' -e '1d' index.md
    ```
4. 关于`git`的`--assume-unchanged` 和 `--skip-worktree`区别
> [Git - Difference Between 'assume-unchanged' and 'skip-worktree'](https://stackoverflow.com/questions/13630849/git-difference-between-assume-unchanged-and-skip-worktree)
>
> `assume-unchanged` is designed for cases where it is expensive to check whether a group of files have been modified; when you set the bit, `git` (of course) assumes the files corresponding to that portion of the index have not been modified in the working copy. So it avoids a mess of `stat` calls. This bit is lost whenever the file's entry in the index changes (so, when the file is changed upstream).
>
> `skip-worktree` is more than that: even where `git` _knows_ that the file has been modified (or needs to be modified by a `reset --hard` or the like), it will pretend it has not been, using the version from the index instead. This persists until the index is discarded.
>
> [知乎](https://www.zhihu.com/question/25234996)
>
> `.gitignore`：这个忽略文件会上传到远程仓库，会使所有人都按照这个文件规则忽略文件；
>
> `.git/info/exclude`：这个会删除远程仓库忽略文件，保留本地文件；
>
> `assume-unchanged`：这个会关闭文件与远程仓库的跟踪，认为这个文件远程仓库是不会修改，所以每次pull都是本地的文件；
>
> `skip-worktree`：这个不会关闭文件与远程仓库的跟踪，只是告诉Git不要跟踪对本地文件/文件夹的更改。
>
> 显示虽然修改但不被`git`染指的文件：
>
> `git ls-files -v . | grep "^S"`

## win10 Powershell快捷键映射
#### ctrl-k, ctrl-u, ctrl-a, ctrl-e, ctrl-b, ctrl-f, etc
```powershell
Import-Module PSReadLine
Set-PSReadLineOption -EditMode Emacs
```
#### 查看环境变量
win `type env:path`  
linux `echo $PATH | tr '\n'`
