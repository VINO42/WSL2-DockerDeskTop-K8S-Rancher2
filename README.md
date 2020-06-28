# WSL2-DockerDeskTop-K8S-Rancher2



在Windows10下使用docker-desktop 结合WSL2  rancher2 构建本地K8S环境，简单高效,排版比较乱，静心看。

* 准备

  * windows10 Pro WSL2
  * Windows Sub Linux System Ubuntu
  * docker-desktop stable 2.3.0.3
  * mobaxterm 
  * wsl2host

* 启用WSL2

  * 将windows 专业版升级到最新版本 笔者是专业工作站版

    ![image-20200628235614044](https://github.com/Andreby42/WSL2-DockerDeskTop-K8S-Rancher2/blob/master/imgs/image-20200628235614044.png)

  * 开启预览者体验计划

    `WSL2是微软在今年6月在18917预览版中发布的，各方面性能都有很大的提升，想尝鲜的同学可以升级Windows试一下，注意：WSL2只能在18917之后的版本中才有。更多关于WSL2的介绍可以在下面的视频中获悉。`

    注意下图中的预览体验成员设置选择慢（推荐）

    ![image-20200628235826566](https://github.com/Andreby42/WSL2-DockerDeskTop-K8S-Rancher2/blob/master/imgs/image-20200628235826566.png)

    之后会有多次重启更新，Don‘t Panic

  * 开启WSL2 网上有很多WSL2的开启的教程 这里简单的写下

    必要条件

    - 运行 Windows 10（[已更新到版本 2004](ms-settings:windowsupdate) 的**内部版本 19041** 或更高版本）。
    - 通过按 Windows 徽标键 + R，检查你的 Windows 版本，然后键入 **winver**，选择“确定”。 （或者在 Windows 命令提示符下输入 `ver` 命令）。 如果内部版本低于 19041，请[更新到最新的 Windows 版本](ms-settings:windowsupdate)。 [获取 Windows 更新助手](https://www.microsoft.com/software-download/windows10)。

    按住Win+X 以管理员方式启动powershell 输入

    ```
    Enable-WindowsOptionalFeature -Online -FeatureName VirtualMachinePlatform 
    ```

    再次输入

    ```
    Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
    ```

    如果需要重启就让电脑自动重启

    我记得这之间 可能会出现

    ```WSL 2 需要更新其内核组件。有关信息，请访问 https://aka.ms/wsl2kernel```

    访问下载``` wsl_update_x64.msi ``` 点击安装

    安装完毕后在ps中输入

    ````
    wsl --set-default-version 2
    ````

    查看当前已安装的Linux版本和名称

    ````
    wsl -l -v
    ````

    配置wsl.config

    打开```C:\Users\\${当前用户}```,新建一个没有后缀名的文件，命名为.wslconfig

    简单的配置限定 wsl使用内存为4GB

    内容如下:

    ```
    [wsl2]
    memory=4GB
    swap=0
    localhostForwarding=true
    ```

  * 安装Ubuntu

    打开开始菜单 找到 windows store , 搜索框输入 Linux 找到Ubuntu，点击安装

    安装完后 点击启动， 我默认使用root用户  就不设置用户名密码。

    配置apt源

    ```
    cp /etc/apt/source.list /etc/apt/source.list.bak
    rm -rf /etc/apt/source.list
    vim /etc/apt/source.list
    ```

    ```
    #阿里源
        deb http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
        deb http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
        deb http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
        deb http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
        deb http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
        deb-src http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
        deb-src http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
        deb-src http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
        deb-src http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
        deb-src http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
    
        #网易源
        deb http://mirrors.163.com/ubuntu/ bionic main restricted universe multiverse
        deb http://mirrors.163.com/ubuntu/ bionic-security main restricted universe multiverse
        deb http://mirrors.163.com/ubuntu/ bionic-updates main restricted universe multiverse
        deb http://mirrors.163.com/ubuntu/ bionic-proposed main restricted universe multiverse
        deb http://mirrors.163.com/ubuntu/ bionic-backports main restricted universe multiverse
        deb-src http://mirrors.163.com/ubuntu/ bionic main restricted universe multiverse
        deb-src http://mirrors.163.com/ubuntu/ bionic-security main restricted universe multiverse
        deb-src http://mirrors.163.com/ubuntu/ bionic-updates main restricted universe multiverse
        deb-src http://mirrors.163.com/ubuntu/ bionic-proposed main restricted universe multiverse
        deb-src http://mirrors.163.com/ubuntu/ bionic-backports main restricted universe multiverse
    
        #清华源
        deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic main restricted universe multiverse
        deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic main restricted universe multiverse
        deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-updates main restricted universe multiverse
        deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-updates main restricted universe multiverse
        deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-backports main restricted universe multiverse
        deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-backports main restricted universe multiverse
        deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-security main restricted universe multiverse
        deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-security main restricted universe multiverse
        deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-proposed main restricted universe multiverse
        deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-proposed main restricted universe multiverse
    
        ##中科大源
        deb https://mirrors.ustc.edu.cn/ubuntu/ bionic main restricted universe multiverse
        deb-src https://mirrors.ustc.edu.cn/ubuntu/ bionic main restricted universe multiverse
        deb https://mirrors.ustc.edu.cn/ubuntu/ bionic-updates main restricted universe multiverse
        deb-src https://mirrors.ustc.edu.cn/ubuntu/ bionic-updates main restricted universe multiverse
        deb https://mirrors.ustc.edu.cn/ubuntu/ bionic-backports main restricted universe multiverse
        deb-src https://mirrors.ustc.edu.cn/ubuntu/ bionic-backports main restricted universe multiverse
        deb https://mirrors.ustc.edu.cn/ubuntu/ bionic-security main restricted universe multiverse
        deb-src https://mirrors.ustc.edu.cn/ubuntu/ bionic-security main restricted universe multiverse
        deb https://mirrors.ustc.edu.cn/ubuntu/ bionic-proposed main restricted universe multiverse
        deb-src https://mirrors.ustc.edu.cn/ubuntu/ bionic-proposed main restricted universe multiverse
      
    ```

    更新系统

    ```
    apt-get update
    
    apt-get upgrade
    
    apt install net-tools
    ```

    配置wsl子系统

    ```
    vim /etc/wsl.conf
    
    [automount]
    enabled = true
    root = /
    options = "metadata"
    [network]
    generateHosts = true
    generateResolvConf = true
    ```

    这样windows盘符会挂载到/下

    ![image-20200629002523354](https://github.com/Andreby42/WSL2-DockerDeskTop-K8S-Rancher2/blob/master/imgs/image-20200629002523354.png)

    到这里子系统安装完毕

  * 安装MobaXterm 主要是这个工具自动识别wsl 喜欢windowsTerminal的也可以在商店下载

    ```
    https://www.sdbeta.com/wg/2019/0729/230718.html
    ```

  * 配置wsl2host

    wsl每次电脑重启后ip会变动，github大神写了个脚本

    ```
    git clone https://github.com/shayne/go-wsl2-host
    
    cd go-wsl2-host
    
    
    > .\wsl2host.exe install
    Windows Username: <username-you-use-to-login-to-windows>
    Windows Password: <password-for-this-user>
    ```

    他这个脚本有点问题，默认的windows hosts文件会有一些空白行或者注释行，那么就会报错

    一定要保证hosts文件第一行不是空白行或者注释行。

    安装服务完毕，进入系统服务列表查看是否存在 wsl2host 服务正在自动运行 如果存在 那么重启电脑如果没有 按照官方wiki操作(自行百度windows 服务卸载 sc delete wsl2host )

    ![image-20200629004149284](https://github.com/Andreby42/WSL2-DockerDeskTop-K8S-Rancher2/blob/master/imgs/image-20200629004149284.png)

    重启完毕后 打开hosts 你会发现多了一行

    ```
    172.18.154.142 ubuntu.wsl    # managed by wsl2-host
    ```

    配置windows上的hosts ,这个很重要,后面k8s搭建要用,hosts文件添加如下一行

    ```
    127.0.0.1 kubernetes.docker.internal
    ```

    

  * 安装docker-desktop

    下载stable 就够了，edge版本会有各种毛病

    ![image-20200629001829667](https://github.com/Andreby42/WSL2-DockerDeskTop-K8S-Rancher2/blob/master/imgs/image-20200629001829667.png)

    配置 docker-desktop

    ![image-20200629001921367](https://github.com/Andreby42/WSL2-DockerDeskTop-K8S-Rancher2/blob/master/imgs/image-20200629001921367.png)

    ![image-20200629001933926](https://github.com/Andreby42/WSL2-DockerDeskTop-K8S-Rancher2/blob/master/imgs/image-20200629001933926.png)

    ![image-20200629001944373](https://github.com/Andreby42/WSL2-DockerDeskTop-K8S-Rancher2/blob/master/imgs/image-20200629001944373.png)

    配置完毕 重启并进入Linux子系统 查看 输入

    ```
    docker ps -a 
    ```

  * 本地install k8s镜像

    ```
    git clone git@github.com:AliyunContainerService/k8s-for-docker-desktop.git
    
    cd k8s-for-docker-desktop.git
    
    git checkout v1.16.5 ## 注意这里对应你的docker-desktop的k8s版本
    	
     .\load_images.ps1
    ```

    等待安装完毕

  * 开启K8S

    当然笔者这里已经开启了。开启完毕会看到左下角的kubernetes is starting.

    ![image-20200629002235214](https://github.com/Andreby42/WSL2-DockerDeskTop-K8S-Rancher2/blob/master/imgs/image-20200629002235214.png)

    切换运行上下文

    ```
    kubectl config use-context docker-desktop
    ```

    验证集群

    ```
    kubectl cluster-info
    
    
    Kubernetes master is running at https://kubernetes.docker.internal:6443
    KubeDNS is running at https://kubernetes.docker.internal:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
    ```

    ```
    kubectl config view
    
    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority-data: DATA+OMITTED
        server: https://kubernetes.docker.internal:6443
      name: docker-desktop
    contexts:
    - context:
        cluster: docker-desktop
        user: docker-desktop
      name: docker-desktop
    - context:
        cluster: docker-desktop
        user: docker-desktop
      name: docker-for-desktop
    current-context: docker-desktop
    kind: Config
    preferences: {}
    users:
    - name: docker-desktop
      user:
        client-certificate-data: REDACTED
        client-key-data: REDACTED
    
    ```

    ```
    kubectl get nodes
    
    docker-desktop   Ready    master   44m   v1.16.6-beta.0
    ```

    他自己升级beta了。

  * 安装rancher2

    注意这里的```ubuntu.wsl```就是通过wsl2host 生成的 ，这个很重要

    ```
    sudo docker run -d -v  ubuntu.wsl:/var/lib/rancher/ --restart=unless-stopped -p 10000:80 -p 10443:443 rancher/rancher:stable
    ```

    安装完毕 拉取镜像后 打开

    ```
    127.0.0.1:10443
    ```

    初始化完密码后

    输入绑定地址时候 ip输入你的wsl2host的host 在这里就是 ubuntu.wsl

  * 导入k8s集群到rancher2

    点击添加集群

    ![image-20200629003059308](https://github.com/Andreby42/WSL2-DockerDeskTop-K8S-Rancher2/blob/master/imgs/image-20200629003059308.png)

    导入

    ![image-20200629003112288](https://github.com/Andreby42/WSL2-DockerDeskTop-K8S-Rancher2/blob/master/imgs/image-20200629003112288.png)

    命名并创建

    ![image-20200629003139219](https://github.com/Andreby42/WSL2-DockerDeskTop-K8S-Rancher2/blob/master/imgs/image-20200629003139219.png)

    出现导入命令行

    ![image-20200629003207503](https://github.com/Andreby42/WSL2-DockerDeskTop-K8S-Rancher2/blob/master/imgs/image-20200629003207503.png)

    打开子系统

    输入

    ```
    kubectl create clusterrolebinding cluster-admin-binding --clusterrole cluster-admin --user [USER_ACCOUNT]
    ```

    [USER_ACCOUNT] 为 kuctl config view 中的集群的用户名称

    再次输入,下面命令第一次输入会报错，不用管，再次输入就ok，注意子系统的host

    ```
    curl --insecure -sfL https://ubuntu.wsl:10443/v3/import/m7zrqhkjqpr2ln64lmjldnlwgtr5djsm4mlwnjd97zj4qhwfnlvk47.yaml | kubectl apply -f -
    
    ```

    静静等待5-10分钟

    最后

    ![image-20200629003425047](https://github.com/Andreby42/WSL2-DockerDeskTop-K8S-Rancher2/blob/master/imgs/image-20200629003425047.png)

    ## 啊 金色传说！

    有问题多问百度谷歌 ,多搜Issues

    相关资源：

    []: https://github.com/shayne/go-wsl2-host
    
    []: https://github.com/AliyunContainerService/k8s-for-docker-desktop

    []: https://github.com/docker/for-win
    
    