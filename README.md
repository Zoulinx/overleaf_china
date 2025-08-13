# 国内自建服务器部署overleaf

## 服务器选择

**阿里云服务器**

阿里云对高校认证的用户提供免费的额度：300/年 可以租最低配的2G内存服务器

[阿里云高校认证](https://www.aliyun.com/benefit/select/gaoxiao)

操作系统选择**ubuntu22.04** 

面板默认**宝塔linux**

1.  进入服务器后，首先更新:

        apt update
        apt install -y docker-compose
      **注意**：这里阿里云服务器自动配置好了阿里源，如果是自己的服务器可能需要换源，否则更新可能会出问题

2.  克隆[overleaf-toolkit](https://github.com/overleaf/toolkit)仓库

        mkdir overleaf-toolkit
        git clone https://github.com/overleaf/toolkit.git ./overleaf-toolkit
        cd ./overleaf-toolkit
    
3.  执行初始化

        ./bin/init
      运行完该指令之后应该会创建出三个配置文件，分别是： *config/overleaf.rc* ， *config/variables.env* 以及 *config/version* ，分别对应Overleaf容器的配置，docker的环境配置以及选用的Overleaf的docker镜像

4.  使用vim修改配置文件

        sudo vim config/overleaf.rc
    一共需要修改4个地方：

    >  ip=0.0.0.0

    >  SIBLING_CONTAINERS_ENABLED=false

    >  OVERLEAF_IMAGE_NAME=wang1zhen/sharelatex-full-with-fonts
    
    这里修改docker镜像（OVERLEAF_IMAGE_NAME）的原因是默认的sharelatex本身的包比较少，
    如果后续使用

        tlmgr install scheme-full
    
    安装的话，4700+个包极其容易卡住，所以更好的方法是直接打包好的docker容器，例如：
    [sharelatex-full](https://github.com/tuetenk0pp/sharelatex-full)

    然而该库的[issue](https://github.com/tuetenk0pp/sharelatex-full/issues/72#issuecomment-2555964857)提到仍然存在中文字体缺少的问题，
    因此这里采用该issue下的热心老哥wangzhen打包的镜像
    
        https://github.com/wang1zhen/sharelatex-full-with-fonts

5.  配置docker的镜像源。这一步是个巨坑，这里首先推荐按照[dockerhub](https://github.com/dongyubin/DockerHub)的建议配置，
然而个人上传的镜像并不容易被加速，且该镜像非常大，非常容易因为网络原因失败，如果失败可以考虑使用付费的轩辕镜像下载个人源：

        https://xuanyuan.cloud/r/wang1zhen/sharelatex-full-with-fonts

    按照轩辕镜像的推荐方法直接安装*sharelatex-full-with-fonts*后，需要修改*config/version*文件的版本号与其保持一致，
    截至2025/8/13，版本号为5.3.2 具体的版本号可以从老哥的镜像名称中推断

6.  调用docker-compose创建所需要的容器

        ./bin/up

    这一步如果出问题，就是镜像源没有配置好的问题，如果我们事先通过docker安装好了*wang1zhen/sharelatex-full-with-fonts*，
    这一步将直接跳过镜像的下载

7.  修改服务器的防火墙，设置0.0.0.0可以随便通过，具体设置可以自行Google。设置完成后复制服务器的IP替换到如下链接并访问验证服务是否启动：

        http://你的ip/launchpad
    
8.  如果正常启动，那么就可以

        ./bin/start

    让程序进入后台长期运行

## 🎉🎉🎉如果走到这都没报错，恭喜你拥有了自己的overleaf服务器，再也不用担心多人协作昂贵的价格以及投稿前连不上网~~~
   
