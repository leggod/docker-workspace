## 介绍

workspace 努力简化创建开发环境过程。
它包含预包装 Docker 镜像，提供你一个美妙的开发环境而不需要安装 PHP, NGINX, MySQL, Node 和其他任何软件在你本地机器上。

**使用概览：**

让我们了解使用它安装 `NGINX`, `PHP`, `Composer`, `MySQL` 和 `Redis`，然后运行 `workspace`

1. 将 workspace 放到你的项目同级文件夹中：
```bash
git clone https://github.com/leggod/workspace.git
```

2. 进入 workspace 目录
 ```bash
cp .env.example .env

docker-compose up -d
```

## 有用的提示

#### 镜像默认存放位置

- Linux:
  cd /var/lib/docker

- MacOS:
  容器和镜像在如下目录下,不同版本或许可能文件版本不一样
  /Users/xxxxmyname/Library/Containers/com.docker.docker/Data
  可以到上面的目录中，查看文件大小, du -sh *
  本机存放位置如下
  /Users/xxxxmyname/Library/Containers/com.docker.docker/Data/vms/0/data/Docker.raw
  也可以通过图形界面
  点击Preferences进入配置界面，然后找到Resources->ADVANCED，找到Disk image location即可

- Windows:

  打开界面设置，

  Settings -> Advanced -> Disk image location

#### 镜像加速

```
"registry-mirrors": [
  "https://2rokrgxv.mirror.aliyuncs.com"
]
```