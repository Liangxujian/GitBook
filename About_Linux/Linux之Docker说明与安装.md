# Linux 之 Docker 说明与安装

## 一、Docker 说明

### 1.1 镜像是什么

镜像是一种轻量级、可执行的独立软件包，用来打包软件运行环境和基于运行环境开发的软件，它包含运行某个软件所需的所有内容，包括代码、运行时、库、环境变量和配置文件。

### 1.2 镜像加载原理

#### 1.2.1 UnionFS（联合文件系统）

UnionFS（联合文件系统）是一种分层、轻量级并且高性能的文件系统，它支持对文件系统的修改作为一次提交来一层层的叠加，同时可以将不同目录挂载到同一个虚拟文件系统下（unite serveral directories into a single virtual filesystem）。Union 文件系统是 Docker 镜像的基础。镜像可以通过分层来进行继承，基于基础镜像（没有父镜像），可以制作各种具体的应用镜像。

特性：以此同时加载多个文件系统，但从外面看起来，只能看到一个文件系统，联合加载会把各层文件叠加起来，这样最终的文件系统会包含所有底层的文件和目录。

#### 1.2.2 镜像加载原理

docker 的镜像实际上由一层一层的文件系统组成，这种层级的文件系统 UnionFS。

bootfs（boot file system）主要包含 BootLoader 和 kernel，BootLoader 主要是引导加载 kernel，Linux 刚启动时回家再 bootfs 文件系统，在 Docker 镜像的最底层是 bootfs。这一层与我们典型的 Linux/Unix 系统是一样的，包含 boot 加载器和内核。当 boot 加载完成之后整个内核就都在内存中了，此时内存的使用权已由 bootfs 转交给内核，此时系统也会卸载 bootfs。

rootfs（root file system），在 bootfs 之上。包含的就是典型 Linux 系统中的 /dev、/proc、/bin、/etc 等标准目录和文件。rootfs 就是各种不同的操作系统发行版，比如 Ubuntu，CentOS 等等。

![image-20210202161813826](.\assets\image-20210202161813826.png)

例如：对于一个精简的 OS，rootfs 可以很小，只需要包含最基本的命令，工具和程序库就可以了，因为底层直接用 Host 的 kernel，自己只需要提供 rootfs 就可以了。由此可见对不同的 Linux 发行版，bootfs 基本是一致的，rootfs 会有差别，因此不同的发行版可以公用 bootfs。

#### 1.2.3 分层的理解

在下载镜像的时候可以观察日志输出，可以看到是一层一层的在下载。

![image-20210202163424571](.\assets\image-20210202163424571.png)

思考：为什么 Docker 镜像要采用这种分层的结构？

最大的好处就是资源共享，比如有多个镜像都从相同的 Base 镜像构建而来，那么宿主机只需在磁盘上保留一份 base 镜像，同时内存中也只需要加载一份 base 镜像，这样就可以为所有的容器服务了，而且镜像的每一层都可以被共享。

```shell
# 查看镜像分层
[root@localhost ~]# docker image inspect tomcat:8.0
[
    {
        "Id": "sha256:ef6a7c98d192507d6066dcf24e44bec66d07ec9cf7c55d8d3d1ea0a24660bdef",
        "RepoTags": [
            "tomcat:8.0"
        ],
        "RepoDigests": [
            "tomcat@sha256:8ecb10948deb32c34aeadf7bf95d12a93fbd3527911fa629c1a3e7823b89ce6f"
        ],
        "Parent": "",
        "Comment": "",
        "Created": "2018-09-12T20:42:22.931362721Z",
        "Container": "537db43b9934d84b08d1226d9e86480c27d0e76eec5dd044cba72a8edc8e193f",
        "ContainerConfig": {
            "Hostname": "537db43b9934",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "8080/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/tomcat/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "LANG=C.UTF-8",
                "JAVA_HOME=/docker-java-home/jre",
                "JAVA_VERSION=7u181",
                "JAVA_DEBIAN_VERSION=7u181-2.6.14-1~deb8u1",
                "CATALINA_HOME=/usr/local/tomcat",
                "TOMCAT_NATIVE_LIBDIR=/usr/local/tomcat/native-jni-lib",
                "LD_LIBRARY_PATH=/usr/local/tomcat/native-jni-lib",
                "OPENSSL_VERSION=1.1.0f-3+deb9u2",
                "GPG_KEYS=05AB33110949707C93A279E3D3EFE6B686867BA6 07E48665A34DCAFAE522E5E6266191C37C037D42 47309207D818FFD8DCD3F83F1931D684307A10A5 541FBE7D8F78B25E055DDEE13C370389288584E7 61B832AC2F1C5A90F0F9B00A1C506407564C17A3 713DA88BE50911535FE716F5208B0AB1D63011C7 79F7026C690BAA50B92CD8B66A3AD3F4F22C4FED 9BA44C2621385CB966EBA586F72C284D731FABEE A27677289986DB50844682F8ACB77FC2E86E29AC A9C5DF4D22E99998D9875A5110C01C5A2F6059E7 DCFD35E0BF8CA7344752DE8B6FB21E8933C60243 F3A04C595DB5B6A5F1ECA43E3B7BBB100D811BBE F7DA48BB64BCB84ECBA7EE6935CD23C10D498E23",
                "TOMCAT_MAJOR=8",
                "TOMCAT_VERSION=8.0.53",
                "TOMCAT_SHA512=cd8a4e48a629a2f2bb4ce6b101ebcce41da52b506064396ec1b2915c0b0d8d82123091242f2929a649bcd8b65ecf6cd1ab9c7d90ac0e261821097ab6fbe22df9",
                "TOMCAT_TGZ_URLS=https://www.apache.org/dyn/closer.cgi?action=download&filename=tomcat/tomcat-8/v8.0.53/bin/apache-tomcat-8.0.53.tar.gz \thttps://www-us.apache.org/dist/tomcat/tomcat-8/v8.0.53/bin/apache-tomcat-8.0.53.tar.gz \thttps://www.apache.org/dist/tomcat/tomcat-8/v8.0.53/bin/apache-tomcat-8.0.53.tar.gz \thttps://archive.apache.org/dist/tomcat/tomcat-8/v8.0.53/bin/apache-tomcat-8.0.53.tar.gz",
                "TOMCAT_ASC_URLS=https://www.apache.org/dyn/closer.cgi?action=download&filename=tomcat/tomcat-8/v8.0.53/bin/apache-tomcat-8.0.53.tar.gz.asc \thttps://www-us.apache.org/dist/tomcat/tomcat-8/v8.0.53/bin/apache-tomcat-8.0.53.tar.gz.asc \thttps://www.apache.org/dist/tomcat/tomcat-8/v8.0.53/bin/apache-tomcat-8.0.53.tar.gz.asc \thttps://archive.apache.org/dist/tomcat/tomcat-8/v8.0.53/bin/apache-tomcat-8.0.53.tar.gz.asc"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "#(nop) ",
                "CMD [\"catalina.sh\" \"run\"]"
            ],
            "ArgsEscaped": true,
            "Image": "sha256:1d3fb924acd246fd2804e5907fd81405abf7cc86072703417daf15bbf551f2e1",
            "Volumes": null,
            "WorkingDir": "/usr/local/tomcat",
            "Entrypoint": null,
            "OnBuild": [],
            "Labels": {}
        },
        "DockerVersion": "17.06.2-ce",
        "Author": "",
        "Config": {
            "Hostname": "",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "8080/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/tomcat/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "LANG=C.UTF-8",
                "JAVA_HOME=/docker-java-home/jre",
                "JAVA_VERSION=7u181",
                "JAVA_DEBIAN_VERSION=7u181-2.6.14-1~deb8u1",
                "CATALINA_HOME=/usr/local/tomcat",
                "TOMCAT_NATIVE_LIBDIR=/usr/local/tomcat/native-jni-lib",
                "LD_LIBRARY_PATH=/usr/local/tomcat/native-jni-lib",
                "OPENSSL_VERSION=1.1.0f-3+deb9u2",
                "GPG_KEYS=05AB33110949707C93A279E3D3EFE6B686867BA6 07E48665A34DCAFAE522E5E6266191C37C037D42 47309207D818FFD8DCD3F83F1931D684307A10A5 541FBE7D8F78B25E055DDEE13C370389288584E7 61B832AC2F1C5A90F0F9B00A1C506407564C17A3 713DA88BE50911535FE716F5208B0AB1D63011C7 79F7026C690BAA50B92CD8B66A3AD3F4F22C4FED 9BA44C2621385CB966EBA586F72C284D731FABEE A27677289986DB50844682F8ACB77FC2E86E29AC A9C5DF4D22E99998D9875A5110C01C5A2F6059E7 DCFD35E0BF8CA7344752DE8B6FB21E8933C60243 F3A04C595DB5B6A5F1ECA43E3B7BBB100D811BBE F7DA48BB64BCB84ECBA7EE6935CD23C10D498E23",
                "TOMCAT_MAJOR=8",
                "TOMCAT_VERSION=8.0.53",
                "TOMCAT_SHA512=cd8a4e48a629a2f2bb4ce6b101ebcce41da52b506064396ec1b2915c0b0d8d82123091242f2929a649bcd8b65ecf6cd1ab9c7d90ac0e261821097ab6fbe22df9",
                "TOMCAT_TGZ_URLS=https://www.apache.org/dyn/closer.cgi?action=download&filename=tomcat/tomcat-8/v8.0.53/bin/apache-tomcat-8.0.53.tar.gz \thttps://www-us.apache.org/dist/tomcat/tomcat-8/v8.0.53/bin/apache-tomcat-8.0.53.tar.gz \thttps://www.apache.org/dist/tomcat/tomcat-8/v8.0.53/bin/apache-tomcat-8.0.53.tar.gz \thttps://archive.apache.org/dist/tomcat/tomcat-8/v8.0.53/bin/apache-tomcat-8.0.53.tar.gz",
                "TOMCAT_ASC_URLS=https://www.apache.org/dyn/closer.cgi?action=download&filename=tomcat/tomcat-8/v8.0.53/bin/apache-tomcat-8.0.53.tar.gz.asc \thttps://www-us.apache.org/dist/tomcat/tomcat-8/v8.0.53/bin/apache-tomcat-8.0.53.tar.gz.asc \thttps://www.apache.org/dist/tomcat/tomcat-8/v8.0.53/bin/apache-tomcat-8.0.53.tar.gz.asc \thttps://archive.apache.org/dist/tomcat/tomcat-8/v8.0.53/bin/apache-tomcat-8.0.53.tar.gz.asc"
            ],
            "Cmd": [
                "catalina.sh",
                "run"
            ],
            "ArgsEscaped": true,
            "Image": "sha256:1d3fb924acd246fd2804e5907fd81405abf7cc86072703417daf15bbf551f2e1",
            "Volumes": null,
            "WorkingDir": "/usr/local/tomcat",
            "Entrypoint": null,
            "OnBuild": [],
            "Labels": null
        },
        "Architecture": "amd64",
        "Os": "linux",
        "Size": 356245923,
        "VirtualSize": 356245923,
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/5467095563d0eeb7800cbe149e38b134094b17d8aa7747a3489b3f4cceed84a8/diff:/var/lib/docker/overlay2/89de18b5ee2a1908787b40e6ad5bd86e42d33296f78800cc54db2884d5c58dd9/diff:/var/lib/docker/overlay2/cbb8e55d674460e4665642025d769aea683b17791cf63befd079ff2330b9970a/diff:/var/lib/docker/overlay2/953860e1c90c9c24927507effc2fdf1ba27d26c218e9739bca296e5104793f62/diff:/var/lib/docker/overlay2/8ddf5a9115e182e660f055eb223454d4159bf54b53258ba9d37fd3bb4369e21e/diff:/var/lib/docker/overlay2/4837f5a37d13a562d57675ee0d9c9fa1ef8edaa93b7b4a28cfce280fedcc3785/diff:/var/lib/docker/overlay2/297ed5eb5bca37cbf115cadd1c301c53b982ed69b79ddfbdbf0c54fa79fd1fcf/diff:/var/lib/docker/overlay2/b57865f8cfdecde7d7db93fe4eca53c71843206bf3299452096051233a515ac8/diff:/var/lib/docker/overlay2/0c28360e0ccb7f91776c5cc66f56b092f27618eda1f42549aac9f831ce73e3f8/diff:/var/lib/docker/overlay2/d159fa1ad10d5869ceaea96ba9b743d9ecd989431e9d9934edace1932f14826e/diff",
                "MergedDir": "/var/lib/docker/overlay2/0576b5f7dd7201c4de9acdf0e52f895efe3b19c33cd4284823a009a22d9fbd86/merged",
                "UpperDir": "/var/lib/docker/overlay2/0576b5f7dd7201c4de9acdf0e52f895efe3b19c33cd4284823a009a22d9fbd86/diff",
                "WorkDir": "/var/lib/docker/overlay2/0576b5f7dd7201c4de9acdf0e52f895efe3b19c33cd4284823a009a22d9fbd86/work"
            },
            "Name": "overlay2"
        },
        "RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:8c466bf4ca6ffdda8b7717b1cd6fe31109529ee64e626a003a224fb8bd2bc469",
                "sha256:daf45b2cad9af3ea091d4376d34fb597a9a67874d48fe5a485e1305df10b3157",
                "sha256:c596d5191368693d366c9b47cc20007efcedd0873691c8895d3684162d469447",
                "sha256:425325c72d902feb9c15b3c9204fc7c24435dfaea2e750db3acda5a11c569049",
                "sha256:fc6174f0df4a7466f749d9ae334872f38336bb761962ba15657b771116cb8546",
                "sha256:39a6e47c4ae6efdd2a0834d81c1bd2860a16cbe5c3c1de28934ee26721bd98c4",
                "sha256:81242e1e644e4d52602431e4c847f4e1ec599761f2ca082cfdf3c38508ddd9d4",
                "sha256:9f052711b40a241eec2783c062a743ad4f7870681024b375714cd277db497ec2",
                "sha256:f26731984f9bb4dc5f286008361729b717d51c2d60989c5e1b2d07d713d9de1f",
                "sha256:583dc95d65c92bd5b5fdb57c640f391435078ff6da2cc9653a7d32457d3cd8b7",
                "sha256:d0f3f4011f287adc910aada9e2fee49c67de366654ea7c02b47d36f9736442fc"
            ]
        },
        "Metadata": {
            "LastTagTime": "0001-01-01T00:00:00Z"
        }
    }
]
```

## 二、Docker 安装

[官方在线文档](https://docs.docker.com/)

````shell
# 1、卸载旧版本
yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine

# 2、下载安装包
yum install -y yum-utils

# 3、设置镜像仓库
yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo # 官网默认使用国外地址，慢
yum-config-manager \
    --add-repo \
    http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo # 国内阿里云镜像加速地址，快

# 更新yum软件包索引
yum makecache fast

# 4、安装Docker
# docker-ce：ce表示社区版；ee表示企业版
yum install docker-ce docker-ce-cli containerd.io

# 5、启动Docker
systemctl start docker

# 6、判断Docker是否安装及启动成功
docker version
````

![image-20201223152056643](D:\GitBook\About_Linux\assets\image-20201223152056643.png)

````shell
# 7、hello-world
docker run hello-world
````

![image-20201223161302881](D:\GitBook\About_Linux\assets\image-20201223161302881.png)

````shell
# 8、查看下载的镜像
docker images
````

## 三、Docker 卸载

````shell
# 卸载docker依赖
yum remove docker-ce docker-ce-cli containerd.io
# 删除docker环境（这里是docker的默认工作路径）
rm -rf /var/lib/docker
````

