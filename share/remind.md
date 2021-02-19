## ssh
- 常用工具: putty, xshell, secureCRT, mobaxterm(win), terminal(ubuntu), zsh(mac)
- host ip: `192.168.31.163`
- host ip: `10.168.20.166`（拨号ip）
- hostname: `lab`
- ssh port: `22`
- account: *name_pinyin*
- passwd_init:`123456`

## sftp
- 常用工具：xftp, mobaxterm, filezilla···

## ssh中运行matlab程序示例
```
%%% in this part, we use % for comment %%%
%复制示例文件，并切换到该目录
%copy example codes to current directory, then changed into the file
cp /home/amax/ana_link/ ~/ana_link
cd ~/ana_link

%在脚本后加上退出命令
%for autoexit, append to your script code.
echo "exit()" >> Linkage_analysis.m 

%后台运行
%run the code backgroud
nohup matlab -nodisplay -nosplash <Linkage_analysis.m 

%查看结果在终端的输出
%debug from the log for console print
cat nohup.out
```
~~#sed -i '$d' fileName~~

## u盘手动挂载示例
```
lsblk 
#查看连接硬盘信息；
如such as sdb1, sdc1, sdd1

udisksctl mount   -b /dev/sdd1  
#挂载；default path to /media/$u/

udisksctl unmount  -b /dev/sdd1
#卸载
```

## Latex 文档编译示例
1. Easyconnect 连接校园网；（已接入校园网内jump到步骤2）
2. 使用shell工具登录个人账户到实验室计算机
ip: `10.168.20.166`, ssh port:`22`
（目前路由器拨号分配的ip，下次拨号很有可能会改变；19楼室内的同学应使用`192.168.31.163`，此为静态ip）
3. 配置编译环境
    - 临时环境：`export PATH=/usr/local/texlive/2019/bin/x86_64-linux:$PATH`
    - 永久环境：添加一行`export PATH=/usr/local/texlivee/2019/bin/x86_64-linux:$PATH` 到 `~/.bashrc`的末尾；  
               执行`source ~/.bashrc`生效。
4. 先得到需要编译的tex文件: `git clone https://github.com/nTnZone/signalOptimal.git` 
5. 进入目录，赋予执行权限: `cd signalOptimal && chmod +x artratex.sh`
6. 利用脚本自动编译（了解参数）: `./artratex.sh x`
7. 编译pdf的目录: `cd Tmp`，在Tmp目录下利用sftp工具取回`main.pdf`

## pt资源下载获取
Deluge: [`lab:8112`](http://lab:8112)
- password: `password`

Transmission: [`lab:9091`](http://lab:9091)
- username:`username`
- password:`password`

Download:
```powershell
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\lab\Courses" -Persist -Credential $cre -Scope Global
New-PSDrive -Name Y -PSProvider FileSystem -Root "\\lab\BT_Movies" -Persist -Credential $cre -Scope Global
```
then, press Windows Key &#8862; + E to open the explorer, find the disk(Z:) or disk(Y:).

## proxy
Clash Webui: [`lab:9080/ui`](http://192.168.31.163:9080/ui)

**chrome** or **firefox**  
插件配置：switchyomega + gfwlist + auto switch

## conda
查看当前环境：`echo $PATH`  
自己手动添加环境变量：`export PATH="/opt/anaconda3/bin:$PATH"`  

## Docker
```
amax@lab:~$ docker image ls
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ros                 kinetic-robot       cbf3a98d8ef7        11 months ago       1.52GB
hello-world         latest              fce289e99eb9        18 months ago       1.84kB
```


## rfid
```
1. *passwd#0 new_passwd#new_passwd#*
2. *passwd#1  (read continuous)#*
3. delete
    - *passwd#20000 #*
    - *passwd#2  (read continuous)#*
3. *passwd#300 #*
4. *passwd#403 #*
5. *passwd#5 passphrase #passphrase#*
```
