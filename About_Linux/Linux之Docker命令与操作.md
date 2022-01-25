# Linux 之 Docker 命令与操作

[docker命令官方文档](https://docs.docker.com/reference/)

## 一、帮助命令

````shell
docker version # 显示docker的版本信息
docker info # 显示docker的系统信息，包括镜像和容器的数量
docker 命令 --help # docker命令帮助
docker images --help
````

## 二、镜像命令

````shell
docker images # 查看所有镜像
# 可选项
  -a, --all             Show all images (default hides intermediate images) # 列出所有镜像
      --digests         Show digests # 略
  -f, --filter filter   Filter output based on conditions provided # 略
      --format string   Pretty-print images using a Go template # 略
      --no-trunc        Don not truncate output # 略
  -q, --quiet           Only show image IDs # 只显示镜像的ID
````

![image-20201223173916935](.\assets\image-20201223173916935.png)

* REPOSITORY：镜像的仓库源
* TAG：镜像的标签
* IMAGE ID：镜像的ID
* ……

````shell
docker search # 搜索docker镜像
# 可选项
  -f, --filter filter   Filter output based on conditions provided
      --format string   Pretty-print search using a Go template
      --limit int       Max number of search results (default 25)
      --no-trunc        Don not truncate output

docker search mysql --filter=stars=3000
````

````shell
docker pull # 下载镜像
docker pull 镜像名[:tag（版本）] # 默认下载最新版
# 如果不写tag指定版本，默认是最新的latest
# 因此以下命令等价
docker pull mysql
docker pull mysql:latest
docker pull docker.io/library/mysql:latest

[root@localhost docker]# docker pull mysql
Using default tag: latest # 如果不写tag指定版本，默认是最新的latest
latest: Pulling from library/mysql
6ec7b7d162b2: Pull complete  # 分层下载（docker image的核心 联合文件系统（UnionFS））
fedd960d3481: Pull complete 
7ab947313861: Pull complete 
64f92f19e638: Pull complete 
3e80b17bff96: Pull complete 
014e976799f9: Pull complete 
59ae84fee1b3: Pull complete 
ffe10de703ea: Pull complete 
657af6d90c83: Pull complete 
98bfb480322c: Pull complete 
6aa3859c4789: Pull complete 
1ed875d851ef: Pull complete 
Digest: sha256:78800e6d3f1b230e35275145e657b82c3fb02a27b2d8e76aac2f5e90c1c30873 # 镜像签名（防伪）
Status: Downloaded newer image for mysql:latest
docker.io/library/mysql:latest # 镜像真实地址

# 分层下载：重复的层不会反复下载，提高速度，减少内存
# 删除镜像的时候也只会删除多余的层而不是全部删掉
[root@localhost docker]# docker pull mysql:5.7
5.7: Pulling from library/mysql
6ec7b7d162b2: Already exists 
fedd960d3481: Already exists 
7ab947313861: Already exists 
64f92f19e638: Already exists 
3e80b17bff96: Already exists 
014e976799f9: Already exists 
59ae84fee1b3: Already exists 
7d1da2a18e2e: Pull complete 
301a28b700b9: Pull complete 
529dc8dbeaf3: Pull complete 
bc9d021dc13f: Pull complete 
Digest: sha256:c3a567d3e3ad8b05dfce401ed08f0f6bf3f3b64cc17694979d5f2e5d78e10173
Status: Downloaded newer image for mysql:5.7
docker.io/library/mysql:5.7
````

````shell
docker rmi # 删除镜像（rmi - remove image）
docker rmi -f 镜像ID # 删除指定镜像
docker rmi -f 镜像ID 镜像ID # 删除指定的多个镜像
docker rmi -f $(docker images -aq) # 递归删除全部镜像
# 可通过REPOSITORY或者IMAGE ID进行删除

[root@localhost docker]# docker rmi -f $(docker images -aq)
Untagged: mysql:latest
Untagged: mysql@sha256:78800e6d3f1b230e35275145e657b82c3fb02a27b2d8e76aac2f5e90c1c30873
Deleted: sha256:a347a59280467629ae4d70ce555e3f96faca8ff2ff8c57cacc8184bebb681f66
Deleted: sha256:8c4db4ce0f63c9eec74a2e555bb7e2fa5e2de08389cbc747793aa30bc3ac04e1
Deleted: sha256:6179be2adc547662cd0ef2cb032b85809ac68923b7ab9c71e5a21b88bbe7f542
Deleted: sha256:17d702350e6aa7f240ca23090d24b059f5c324e292a77b5069225e76e0b51dd6
Deleted: sha256:5c5f95733957fd96300b5265d6b2df728415ed62e7b4b30eadd7d836d48187bf
Deleted: sha256:fbf118fadaf5230c8df9a3b51c608393a8f3adb99c089ea2db253438efab1a0e
Deleted: sha256:43b82d704a10e6d022fa3f31a5f827a00e339ee21dd2849a9b120ab82be9af71
Deleted: sha256:a4994702421d2b9a74c4f3810aa7ac09990e849905f23a1d8f358c826d58501f
Deleted: sha256:36c598c7a6f90abf6d67cde4a58b0747bfbcc7441d3b782bdeea7cda8c9ad7b6
Deleted: sha256:86f598b4f8200bdd4ae752f916154e4e29d5b4c211bb124eb150b9957a3e0141
Deleted: sha256:9e979d97f92bf78a225c77c6b4ba74eb2e03efb95b19b69206cd17cee15a4b26
Deleted: sha256:aff48ce4678f78d83d7e9bfb9e88cd951c3da52da08779e99b6082edd1cc66f3
Deleted: sha256:87c8a1d8f54f3aa4e05569e8919397b65056aa71cdf48b7f061432c98475eee9
Untagged: hello-world:latest
Untagged: hello-world@sha256:1a523af650137b8accdaed439c17d684df61ee4d74feac151b5b337bd29e7eec
Deleted: sha256:bf756fb1ae65adf866bd8c456593cd24beb6a0a061dedf42b26a993176745f6b
````

## 三、容器命令

> 注意：有了镜像才可以创建容器，因此需要先下载一个 centos（这种基础版本的 centos 很多命令都是不完善的）
>
> ````shell
> docker pull centos
> ````

### 3.1 新建容器和启动（run）

````shell
docker run [可选参数] image
# 【如果要运行的镜像不存在，docker会自动执行：docker pull 镜像名称】
# 参数说明
--name="Name"          # 附带名字启动容器（方便区分）
-d                     # 以后台的方式运行（docker容器使用后台运行，就必须要有一个前台进程，docker发现自己没有提供服务，就会自动停止）
-it                    # 使用交互方式运行（即进入容器中进行操作）
-p                     # 指定容器端口启动
    -p ip:主机端口:容器端口
    -p 主机端口:容器端口 # （常用）
    -p 容器端口
    容器端口
-P                     # 随机指定端口

# 示例
[root@localhost ~]# docker run -it --rm centos /bin/bash
# 【--rm表示容器停止后即删除镜像，使用docker ps -a不会找到，这种方法一般用于测试使用】
[root@localhost ~]# docker run -it centos /bin/bash
[root@987ceaa1b6cc /]# ls
bin  etc   lib    lost+found  mnt  proc  run   srv  tmp  var
dev  home  lib64  media       opt  root  sbin  sys  usr

# 从容器中退回主机
[root@987ceaa1b6cc /]# exit
exit
[root@localhost /]# ls
bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
boot  etc  lib   media  opt  root  sbin  sys  usr
````

### 3.2 容器查看命令（ps）

````shell
docker ps [可选参数]
      # 列出当前正在运行的容器
-a    # 列出当前正在运行的容器 + 带出历史运行过的容器
-n=?  # 显示最近创建的几个容器
-q    # 只显示容器的编号

# 示例
[root@localhost /]# docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
[root@localhost /]# docker ps -a
CONTAINER ID   IMAGE          COMMAND       CREATED          STATUS                       PORTS     NAMES
987ceaa1b6cc   centos         "/bin/bash"   33 minutes ago   Exited (127) 5 minutes ago             gallant_stonebraker
0f4a68190c7a   bf756fb1ae65   "/hello"      8 days ago       Exited (0) 8 days ago                  recursing_lovelace
[root@localhost /]# docker ps -n=1
CONTAINER ID   IMAGE     COMMAND       CREATED          STATUS                       PORTS     NAMES
987ceaa1b6cc   centos    "/bin/bash"   33 minutes ago   Exited (127) 5 minutes ago             gallant_stonebraker
````

### 3.3 容器退出命令（exit|^p + ^q）

````shell
exit          # 容器停止并退出
Ctrl + p + q  # 容器不停止退出
````

### 3.4 容器删除命令（rm）

````shell
docker rm 容器ID                 # 根据ID删除指定容器（如果容器正在运行，则不可删除）
docker rm -f 容器ID              # 根据ID强制删除指定容器
docker rm $(docker ps -aq)       # 遍历删除所有容器
docker rm -f $(docker ps -aq)    # 遍历强制删除所有容器
docker ps -a -q|xargs docker rm  # 删除所有容器
````

### 3.5 容器启动和停止（start|stop）

````shell
docker start 容器ID    # 启动容器
docker restart 容器ID  # 重启容器
docker stop 容器ID     # 停止当前容器
docker kill 容器ID     # 强制停止当前容器
docker pause 容器ID    # 暂停当前容器
docker unpause 容器ID  # 启动暂停中的容器
````

## 四、其他常用命令

### 4.1 查看日志（logs）

````shell
docker logs -t -f --detail 显示日志的条数 容器ID
# （-t表示显示时间戳；-f表示持续显示）
[root@localhost ~]# docker logs --help

Usage:  docker logs [OPTIONS] CONTAINER

Fetch the logs of a container

Options:
      --details        Show extra details provided to logs
  -f, --follow         Follow log output
      --since string   Show logs since timestamp (e.g. 2013-01-02T13:23:37Z)
                       or relative (e.g. 42m for 42 minutes)
  -n, --tail string    Number of lines to show from the end of the logs
                       (default "all")
  -t, --timestamps     Show timestamps
      --until string   Show logs before a timestamp (e.g.
                       2013-01-02T13:23:37Z) or relative (e.g. 42m for 42
                       minutes)

# 先编写一段shell脚本作为测试
[root@localhost ~]# docker run -d centos /bin/sh -c "while true;do echo liangxj;sleep 1;done"
22b9beab5965eacb2e8f646c1e096ffd10fa7355df47f3da8ff6244ca7497bfb

[root@localhost ~]# docker ps
CONTAINER ID   IMAGE
22b9beab5965   centos

# 显示日志
[root@localhost ~]# docker logs -tf --tail 10 22b9beab5965
````

### 4.2 查看容器中的进程信息（top）

````shell
docker top 容器ID
[root@localhost ~]# docker top 0388fc715003
UID                 PID                 PPID                C                   STIME               TTY
root                8956                8936                0                   01:38               ?
root                9153                8956                0                   01:40               ?
# PID：进程ID
# PPID：父进程ID
````

### 4.3 查看进程元数据（inspect）

```shell
docker inspect 容器ID

[root@localhost ~]# docker inspect --help

Usage:  docker inspect [OPTIONS] NAME|ID [NAME|ID...]

Return low-level information on Docker objects

Options:
  -f, --format string   Format the output using the given Go template
  -s, --size            Display total file sizes if the type is container
      --type string     Return JSON for specified type

[root@localhost ~]# docker inspect 0388fc715003
[
    {
        "Id": "0388fc715003a52ce493ee6ee915fb670681285fe07d61d35eeed23caa505be6",
        "Created": "2021-01-04T06:38:39.643094366Z",
        "Path": "/bin/sh",
        "Args": [
            "-c",
            "while true;do echo liangxj;sleep 1;done"
        ],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 8956,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2021-01-04T06:38:40.052078891Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:300e315adb2f96afe5f0b2780b87f28ae95231fe3bdd1e16b9ba606307728f55",
        "ResolvConfPath": "/var/lib/docker/containers/0388fc715003a52ce493ee6ee915fb670681285fe07d61d35eeed23caa505be6/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/0388fc715003a52ce493ee6ee915fb670681285fe07d61d35eeed23caa505be6/hostname",
        "HostsPath": "/var/lib/docker/containers/0388fc715003a52ce493ee6ee915fb670681285fe07d61d35eeed23caa505be6/hosts",
        "LogPath": "/var/lib/docker/containers/0388fc715003a52ce493ee6ee915fb670681285fe07d61d35eeed23caa505be6/0388fc715003a52ce493ee6ee915fb670681285fe07d61d35eeed23caa505be6-json.log",
        "Name": "/dreamy_carson",
        "RestartCount": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "default",
            "PortBindings": {},
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "CapAdd": null,
            "CapDrop": null,
            "CgroupnsMode": "host",
            "Dns": [],
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "ConsoleSize": [
                0,
                0
            ],
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": [],
            "BlkioDeviceReadBps": null,
            "BlkioDeviceWriteBps": null,
            "BlkioDeviceReadIOps": null,
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "KernelMemory": 0,
            "KernelMemoryTCP": 0,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": false,
            "PidsLimit": null,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/e2f71bff5cc0b5e32761153742315bdba576839119902c592f601f1162b9bae1-init/diff:/var/lib/docker/overlay2/8508e4e63830c2cd44cc90e39b2bcc4aaf442f8eecf36fa7ef8a343930481fdb/diff",
                "MergedDir": "/var/lib/docker/overlay2/e2f71bff5cc0b5e32761153742315bdba576839119902c592f601f1162b9bae1/merged",
                "UpperDir": "/var/lib/docker/overlay2/e2f71bff5cc0b5e32761153742315bdba576839119902c592f601f1162b9bae1/diff",
                "WorkDir": "/var/lib/docker/overlay2/e2f71bff5cc0b5e32761153742315bdba576839119902c592f601f1162b9bae1/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [],
        "Config": {
            "Hostname": "0388fc715003",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "while true;do echo liangxj;sleep 1;done"
            ],
            "Image": "centos",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "org.label-schema.build-date": "20201204",
                "org.label-schema.license": "GPLv2",
                "org.label-schema.name": "CentOS Base Image",
                "org.label-schema.schema-version": "1.0",
                "org.label-schema.vendor": "CentOS"
            }
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "bb48a599f020d56f908d78b170da712209ac5ff3a185599f21da1bfa30c77ec0",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {},
            "SandboxKey": "/var/run/docker/netns/bb48a599f020",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "bca90a216e8e4eaa7c7d105652509de996a1a9a786c0717758a90db24167ec4d",
            "Gateway": "172.17.0.1",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "172.17.0.2",
            "IPPrefixLen": 16,
            "IPv6Gateway": "",
            "MacAddress": "02:42:ac:11:00:02",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "340b9bbe55d6cd8d6879ade40a0106da1eaec688dd9ef60c74479905422ccc22",
                    "EndpointID": "bca90a216e8e4eaa7c7d105652509de996a1a9a786c0717758a90db24167ec4d",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:11:00:02",
                    "DriverOpts": null
                }
            }
        }
    }
]
```

### 4.4 进入当前正在运行的容器（exec|attach）

```shell
# 一般容器都是使用后台方式运行，然后进入容器，修改配置
# 方式一
docker exec -it 容器ID bashShell
# 示例
[root@localhost ~]# docker ps
CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS         PORTS     NAMES
f88acc152e9e   centos    "/bin/bash"   4 minutes ago   Up 4 minutes             charming_jang
[root@localhost ~]# docker exec -it f88acc152e9e /bin/bash
[root@f88acc152e9e /]# 

# 方式二
docker attach 容器ID
# 示例
[root@localhost ~]# docker ps
CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS         PORTS     NAMES
f88acc152e9e   centos    "/bin/bash"   7 minutes ago   Up 7 minutes             charming_jang
[root@localhost ~]# docker attach f88acc152e9e
[root@f88acc152e9e /]# 

# 对比
docker exec -it 容器ID bashShell  # 进入容器后开启一个新的终端，可以在里面操作（常用）
docker attach 容器ID              # 进入容器正在执行的终端，不会启动新的进程
```

### 4.5 拷贝容器内的文件到主机上（cp）

```shell
docker cp 容器ID:容器内路径 主机上的目标路径
# 注意：无论容器是后台运行抑或是停止了，都可以从容器中把文件拷贝出来
# 示例
[root@localhost ~]# docker ps
CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS         PORTS     NAMES
f88acc152e9e   centos    "/bin/bash"   4 minutes ago   Up 4 minutes             charming_jang
[root@localhost ~]# docker exec -it f88acc152e9e /bin/bash
[root@f88acc152e9e /]# ls  
bin  etc   lib    lost+found  mnt  proc  run   srv  tmp  var
dev  home  lib64  media       opt  root  sbin  sys  usr
[root@f88acc152e9e /]# cd home
[root@f88acc152e9e home]# ls
# 容器内新建文件
[root@f88acc152e9e home]# touch liangxj.txt
[root@f88acc152e9e home]# ls
liangxj.txt
[root@f88acc152e9e home]# exit
exit
[root@localhost ~]# docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
[root@localhost ~]# docker ps -a
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS                      PORTS     NAMES
f88acc152e9e   centos    "/bin/bash"              23 minutes ago   Exited (0) 24 seconds ago             charming_jang
[root@localhost ~]# cd /home
[root@localhost home]# ls
# 拷贝容器内的文件到主机上
[root@localhost home]# docker cp f88acc152e9e:/home/liangxj.txt /home
[root@localhost home]# ls
liangxj.txt

# 注意：这里的拷贝是一个手动的过程，还可以使用 -V 卷的技术，实现容器内的文件夹与主机上的文件夹对应起来，自动同步
```

### 4.6 查看 Docker 运行中的容器的实时数据流

````shell
[root@localhost home]# docker stats
# 可以查看运行中的容器占用的CPU和内存情况，与任务管理器类似
````



## 五、命令小结

![docker命令](.\assets\20200327100642605577.png)

```shell
attach      Attach local standard input, output, and error streams to a running container # 当前shell下attach链接指定运行镜像
build       Build an image from a Dockerfile                                              # 通过Dockerfile定制镜像
commit      Create a new image from a container changes                                   # 提交当前容器为新的容器
cp          Copy files/folders between a container and the local filesystem               # 从容器中拷贝指定文件或者目录到宿主机上
create      Create a new container                                                        # 创建一个新的容器，同run，但不启动容器
diff        Inspect changes to files or directories on a container filesystem             # 查看docker容器编号
events      Get real time events from the server                                          # 从docker服务获取容器实时事件
exec        Run a command in a running container                                          # 在已存在的容器上运行命令
export      Export a container filesystem as a tar archive                                # 导出容器的内容流作为一个tar归档文件[对应import]
history     Show the history of an image                                                  # 展示一个镜像形成历史
images      List images                                                                   # 列出系统当前镜像
import      Import the contents from a tarball to create a filesystem image               # 从tar包中的内容创建一个新的文件系统映像[对应export]
info        Display system-wide information                                               # 显示系统相关信息
inspect     Return low-level information on Docker objects                                # 查看容器详细信息
kill        Kill one or more running containers                                           # kill指定docker容器
load        Load an image from a tar archive or STDIN                                     # 从一个tar包中加载一个镜像[对应save]
login       Log in to a Docker registry                                                   # 注册或者登陆一个docker源服务器
logout      Log out from a Docker registry                                                # 从当前Docker registry退出
logs        Fetch the logs of a container                                                 # 输出当前容器日志信息
pause       Pause all processes within one or more containers                             # 暂停容器
port        List port mappings or a specific mapping for the container                    # 查看映射端口对应的容器内部源端口
ps          List containers                                                               # 列出容器列表
pull        Pull an image or a repository from a registry                                 # 从docker镜像源服务器拉取指定镜像或者库镜像
push        Push an image or a repository to a registry                                   # 推送指定镜像或者库镜像至docker源服务器
rename      Rename a container                                                            # 重命名容器
restart     Restart one or more containers                                                # 重启运行的容器
rm          Remove one or more containers                                                 # 移除一个或者多个容器
rmi         Remove one or more images                                                     # 移除一个或多个镜像[无容器使用该镜像才可删除，否则需删除相关容器才可继续或-f强制删除]
run         Run a command in a new container                                              # 创建一个新的容器并运行一个命令
save        Save one or more images to a tar archive (streamed to STDOUT by default)      # 保存一个镜像为一个tar包[对应load]
search      Search the Docker Hub for images                                              # 在docker hub中搜索镜像
start       Start one or more stopped containers                                          # 启动容器
stats       Display a live stream of container(s) resource usage statistics               # 返回运行中的容器的实时数据流
stop        Stop one or more running containers                                           # 停止容器
tag         Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE                         # 标记本地镜像，将其归入某一仓库
top         Display the running processes of a container                                  # 查看容器中运行的进程信息
unpause     Unpause all processes within one or more containers                           # 取消暂停容器
update      Update configuration of one or more containers                                # 更新一个或多个容器的配置
version     Show the Docker version information                                           # 查看docker版本号
wait        Block until one or more containers stop, then print their exit codes          # 截取容器停止时的退出状态值
```

## 六、练习示例

### 6.1 Docker 安装 Nginx

```shell
# 1、dockerhub搜索镜像信息
# 2、下载镜像
[root@localhost /]# docker pull nginx
# 3、运行镜像
[root@localhost /]# docker images
REPOSITORY   TAG       IMAGE ID       CREATED       SIZE
nginx        latest    ae2feff98a0c   3 weeks ago   133MB
centos       latest    300e315adb2f   4 weeks ago   209MB
# 【这里提前参考Linux之Tomcat的笔记，配置防火墙，使得主机可以联通Linux虚拟机的9999端口】
[root@localhost /]# docker run -d --name nginx01 -p 9999:80 nginx
# 【-d：后台启动；--name：命名容器；-p：端口映射（宿主机端口号:容器端口号）】
6956c87154b0a7ee642719ff22fcb938b8556d5966315ecac81259e3e6c04bf5
docker: Error response from daemon: driver failed programming external connectivity on endpoint nginx01 (3ee4feb43afd6f892828259da60044b35963ff190db3288a45af4f96c14f12cd):  (iptables failed: iptables --wait -t nat -A DOCKER -p tcp -d 0/0 --dport 9999 -j DNAT --to-destination 172.17.0.2:80 ! -i docker0: iptables: No chain/target/match by that name.
 (exit status 1)).
 # 【这里启动报错（driver failed programming external connectivity on endpoint nginx01），因为Linux虚拟机更新并重启了虚拟机的firewall，docker服务启动时定义的自定义链DOCKER从iptables中被清掉了，影响了docker的正常工作，这时需要重启docker（注意启动失败的容器，否则容器名称重复还是会失败）】
[root@localhost /]# systemctl restart docker
[root@localhost /]# docker rm $(docker ps -aq)
6956c87154b0
[root@localhost /]# docker run -d --name nginx01 -p 9999:80 nginx
7f5bfce90a0bc1f594cf90dc853ddf898ddbda51a59da9a4bd591f10846b7a11
# 4、测试容器启动情况
[root@localhost /]# curl localhost:9999
# 【这时，也可以在主机通过浏览器访问到nginx欢迎页面（虚拟机IP:9999）】
# 5、进入容器
[root@localhost /]# docker ps
CONTAINER ID   IMAGE     ……     PORTS                  NAMES
7f5bfce90a0b   nginx     ……     0.0.0.0:9999->80/tcp   nginx01
[root@localhost /]# docker exec -it 7f5bfce90a0b /bin/bash
root@7f5bfce90a0b:/# whereis nginx
nginx: /usr/sbin/nginx /usr/lib/nginx /etc/nginx /usr/share/nginx
# 6、退出容器
root@7f5bfce90a0b:/# exit
exit
# 7、停止容器
[root@localhost /]# docker ps
CONTAINER ID   IMAGE     ……     PORTS                  NAMES
7f5bfce90a0b   nginx     ……     0.0.0.0:9999->80/tcp   nginx01
[root@localhost /]# docker stop 7f5bfce90a0b
7f5bfce90a0b
[root@localhost /]# docker ps
CONTAINER ID   IMAGE     ……     PORTS                  NAMES
# 【停止容器后，在Linux虚拟机curl不可访问nginx，在主机浏览器也无法访问nginx】
```

### 6.2 Docker 安装 Tomcat

```shell
# 1、dockerhub搜索镜像信息
# 2、下载镜像
[root@localhost ~]# docker pull tomcat
# 【或者下载指定的Tomcat版本镜像】
[root@localhost ~]# docker pull tomcat:7.0
# 3、运行镜像
[root@localhost ~]# docker images
REPOSITORY   TAG       IMAGE ID       CREATED       SIZE
tomcat       7.0       096d3f7bdb4a   2 weeks ago   533MB
tomcat       latest    feba8d001e3f   2 weeks ago   649MB
nginx        latest    ae2feff98a0c   3 weeks ago   133MB
centos       latest    300e315adb2f   4 weeks ago   209MB
[root@localhost /]# docker run -d -p 9999:8080 --name tomcat01 tomcat
90b2563cd6b1aead7dba21718d866d6e7f46d667afd1d83441adfd4f6f8ee0e7
[root@localhost /]# docker exec -it tomcat01 /bin/bash
root@90b2563cd6b1:/usr/local/tomcat# ll
bash: ll: command not found
root@90b2563cd6b1:/usr/local/tomcat# ls -a
.             CONTRIBUTING.md  README.md      bin   logs            webapps
..            LICENSE          RELEASE-NOTES  conf  native-jni-lib  webapps.dist
BUILDING.txt  NOTICE           RUNNING.txt    lib   temp            work
# 【注意：】
# 【1、在Tomcat容器中，发现Linux的命令少了（因为是阉割版，只保留最低需求）；】
# 【2、在Tomcat容器里，webapps是一个空的文件夹（因为阿里云里默认是最小的镜像，删除了所有不必要的东西，只保证最小的可运行环境）】
# 【第2点导致，虽然Tomcat容器启动成功了，但是在主机的浏览器中访问首页会报404，找不到目录】
```

![image-20210107144001571](.\assets\image-20210107144001571.png)

```shell
root@90b2563cd6b1:/usr/local/tomcat# cd webapps.dist/
root@90b2563cd6b1:/usr/local/tomcat/webapps.dist# ls
ROOT  docs  examples  host-manager  manager
# 【要解决访问Tomcat首页报404的问题，拷贝webapps.dist文件夹里的内容，到webapps即可】
root@90b2563cd6b1:/usr/local/tomcat# cp -r webapps.dist/* webapps
# 【这里有一个情况，就是Tomcat中的项目文件会经常需要修改，因此可以】
```

![image-20210107144639609](.\assets\image-20210107144639609.png)

### 6.3 Docker 安装 ES 和 Kibana

````shell
# 1、dockerhub搜索镜像信息
# 2、下载镜像并启动elasticsearch
[root@localhost ~]# docker run -d --name elasticsearch -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elasticsearch:7.6.2
# 注意：es暴露的端口很多；es十分耗内存；es的数据一般需要放置到安全目录！挂载
# 3、测试es是否启动成功
[root@localhost ~]# curl localhost:9200
{
  "name" : "eaeb13f0f20a",
  "cluster_name" : "docker-cluster",
  "cluster_uuid" : "LSgGyRUQSdy_MbxUbPyLKw",
  "version" : {
    "number" : "7.6.2",
    "build_flavor" : "default",
    "build_type" : "docker",
    "build_hash" : "ef48eb35cf30adf4db14086e8aabd07ef6fb113f",
    "build_date" : "2020-03-26T06:34:37.794943Z",
    "build_snapshot" : false,
    "lucene_version" : "8.4.0",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
# 4、查看docker中运行镜像的状态（每秒刷新），可以看到es十分占内存
[root@localhost ~]# docker stats
CONTAINER ID   NAME            CPU %     MEM USAGE / LIMIT     MEM %     NET I/O     BLOCK I/O        PIDS
eaeb13f0f20a   elasticsearch   0.72%     1.234GiB / 1.781GiB   69.32%    648B / 0B   401MB / 1.76MB   46
# 5、增加内存限制，一般是通过修改es配置文件实现，而在docker中则通过启动是加入-e进行环境配置修改
[root@localhost ~]# docker run -d --name elasticsearch -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" -e ES_JAVA_OPTS="-Xms64m -Xmx512m" elasticsearch:7.6.2
# 6、检查es是否启动成功
[root@localhost ~]# curl localhost:9200
{
  "name" : "4901e48b16b2",
  "cluster_name" : "docker-cluster",
  "cluster_uuid" : "Vvn5-B7xRBquRfiUZFqI5g",
  "version" : {
    "number" : "7.6.2",
    "build_flavor" : "default",
    "build_type" : "docker",
    "build_hash" : "ef48eb35cf30adf4db14086e8aabd07ef6fb113f",
    "build_date" : "2020-03-26T06:34:37.794943Z",
    "build_snapshot" : false,
    "lucene_version" : "8.4.0",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
# 7、查看es的运行状态
[root@localhost ~]# docker stats 4901e48b16b2
CONTAINER ID   NAME            CPU %     MEM USAGE / LIMIT     MEM %     NET I/O     BLOCK I/O        PIDS
4901e48b16b2   elasticsearch   0.32%     402.6MiB / 1.781GiB   22.08%    648B / 0B   190MB / 1.75MB   47
````

## 七、Docker 可视化操作

### 7.1 Portainer（可视化容器管理工具）

```shell
docker run -d -p 9999:9000 --restart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer
```

访问测试：

```shell
# 虚拟机
curl localhost:9999
# 本机访问
虚拟机IP:9999
```

![image-20210202112817927](.\assets\image-20210202112817927.png)

在界面中创建账号和密码：admin - admin@123

![image-20210202112948591](.\assets\image-20210202112948591.png)

在界面中选择`Local`，然后点击`Connect`进入管理面板

![image-20210202114634718](D:\GitBook\About_Linux\assets\image-20210202114634718.png)

一般不用可视化操作界面，界面信息及操作参考博客：[可视化图形工具Portainer](http://www.yunweipai.com/34991.html)

### 7.2 Rancher（Docker 容器管理系统）

> 持续集成与持续部署（CI/CD）

