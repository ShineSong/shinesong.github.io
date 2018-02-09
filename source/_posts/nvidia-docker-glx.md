---
title: Nvidia-docker2下使用glx
date: 2017-12-11 10:42:11
tags: [docker,vgl,glx]
categories: Trick
---

为了想在Docker中使用Gazebo， RVIZ这样需要GLX渲染的应用， 啃起了nvidia-docker 1和2.

nvidia-docker比较迷,1支持的东西,到了2.0反而不支持了,而且并不打算支持(在issue里声称正在做opengl的支持,但不打算支持glx).

虽然不知道nvidia-docker 2.0究竟比1.0牛逼在了哪儿,但是总觉得直接全面换成1.0不是很行,所以最后折腾了一天终于搞定了这两个磨人的小妖精.

## 1.0和2.0共存

根据官方wiki的说法,这个是可行的,nvidia-docker2.0分成了两个包, nvidia-docker2 和nvidia-runtime-container. 其实仅需要container这个包,利用docker手动注册到daemon.json里面,在运行的时候使用`docker run --runtime="nvidia" blabla`即可.

所以我们先根据官方指引,从他的repo里安装nvidia-docker2, 然后`apt remove nvidia-docker2`, 再找到1.0的branch,下载1.0对应的deb,用dpkg安装即可

## 创建镜像

创建镜像对1.0和2.0没有任何区别,在官方Dockerfile的注释里还有 "For nvidia-docker 1.0"的字样. 所以创建镜像就直接

    FROM nvidia/cuda

就好了.

## 运行nvidia-docker 1.0 并调用glxgears

nvidia-docker 1.0 必须用nvidia-docker来运行.手动将plugin注册到daemon.json并不好使.当然也可能我使用的姿势不对.下面是我的启动脚本

    #!/bin/zsh

    HOMEDIR=/home/sxs/DRL/docker/dockerws/
    WORKSPACE=/home/sxs/DRL/docker/marioai
    xhost +local:root
    nvidia-docker run -it --rm \
        --privileged \
        --env="DISPLAY" \
        --volume="/tmp/.X11-unix:/tmp/.X11-unix:rw" \
        --volume="/usr/lib/x86_64-linux-gnu/libXv.so.1:/usr/lib/x86_64-linux-gnu/libXv.so.1" \
        --volume="$HOMEDIR:/home/dock:rw" \
        --volume="$WORKSPACE:/home/dock/workspace:rw" \
        -u 1000 \
        --workdir "/home/dock/workspace/" \
        --network=host \
        sxs/cuda-ros /bin/zsh

    xhost -local:root

启动之后可以放心的使用glxgears,可以看到几个飞速旋转的小齿轮.
这里有一个trick,当设置network以后,必须加上--privileged才能正确的显示出来,否则只有窗口但是一片漆黑.

## 运行nvidia-docker 2.0

虽然不知道nvidia-docker2到底比1强在哪儿,但姑且能运行起来先.

    #!/bin/zsh

    HOMEDIR=/home/sxs/DRL/docker/dockerws/
    WORKSPACE=/home/sxs/DRL/docker/marioai
    xhost +local:root
    docker run -it --rm \
        --privileged \
        --runtime="nvidia" \
        --env="DISPLAY" \
        --volume="/tmp/.X11-unix:/tmp/.X11-unix:rw" \
        --volume="/usr/lib/x86_64-linux-gnu/libXv.so.1:/usr/lib/x86_64-linux-gnu/libXv.so.1" \
        --volume="$HOMEDIR:/home/dock:rw" \
        --volume="$WORKSPACE:/home/dock/workspace:rw" \
        -u 1000 \
        --workdir "/home/dock/workspace/" \
        --network=host \
        sxs/cuda-ros /bin/zsh

    xhost -local:root

## 总结

Docker真是个有趣的东西,方便且优雅,用好了它可以解放掉多少配环境的时间.隔离的环境也非常有利于调试和部署.到现在还琢磨出自己的使用方法,使用起来还是很蹩脚.生命不息,折腾不止.