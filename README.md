1、关闭防火墙
$ systemctl stop firewalld.service
$ systemctl disable firewalld.service


2、安装配置 nfs
$ yum -y install nfs-utils rpcbind


3、共享目录设置权限：
$ chmod 755 /data/k8s/


4、配置 nfs，nfs 的默认配置文件在 /etc/exports 文件下，在该文件中添加下面的配置信息：
$ vi /etc/exports/data/k8s  *(rw,sync,no_root_squash)


5、配置说明：
/data/k8s：是共享的数据目录
*：表示任何人都有权限连接，当然也可以是一个网段，一个 IP，也可以是域名
rw：读写的权限
sync：表示文件同时写入硬盘和内存
no_root_squash：当登录 NFS 主机使用共享目录的使用者是 root 时，其权限将被转换成为匿名使用者，通常它的 UID 与 GID，都会变成 nobody 身份
启动服务 nfs 需要向 rpc 注册，rpc 一旦重启了，注册的文件都会丢失，向他注册的服务都需要重启
注意启动顺序，先启动 rpcbind

$ systemctl start rpcbind.service
$ systemctl enable rpcbind
$ systemctl status rpcbind
看到上面的 Started 证明启动成功了。


然后启动 nfs 服务：
$ systemctl start nfs.service
$ systemctl enable nfs
$ systemctl status nfs
同样看到 Started 则证明 NFS Server 启动成功了。

另外还可以通过下面的命令确认下：
$ rpcinfo -p|grep nfs
    100003    3   tcp   2049  nfs
    100003    4   tcp   2049  nfs
    100227    3   tcp   2049  nfs_acl
    100003    3   udp   2049  nfs
    100003    4   udp   2049  nfs
100227    3   udp   2049  nfs_acl


查看具体目录挂载权限：
$ cat /var/lib/nfs/etab/data/k8s    *(rw,sync,wdelay,hide,nocrossmnt,secure,no_root_squash,no_all_squash,no_subtree_check,secure_locks,acl,no_pnfs,anonuid=65534,anongid=65534,sec=sys,secure,no_root_squash,no_all_squash)



1. nfs-client.yaml

2. nfs-client-sa.yaml

3 .nfs-client-class.yaml

$ kubectl create -f nfs-client.yaml
$ kubectl create -f nfs-client-sa.yaml
$ kubectl create -f nfs-client-class.yaml

--------------------------------------------

1. jenkins_rbac.yaml

2. jenkins_statefulset.yml

3. jenkins_service.yml

4. jenkins_ingress.yml





























































