---
title: 使用Eclipse与RSE进行远程ROS开发
date: 2018-01-17 16:42:36
tags: [ide, ros]
categories: Trick
---

# Generate Eclipse Project for Catkin packages
Run this command which can let your cmake output verbose definition, c++11 macro to CDT4 project.

    catkin config --cmake-args -DCMAKE_BUILD_TYPE=Release -DCMAKE_VERBOSE_MAKEFILE=ON -G"Eclipse CDT4 - Unix Makefiles" -DCMAKE_CXX_FLAGS="-std=c++11 -D_cplusplus=201103L"

to generate the .project files for each package and then run: the following script

    ROOT=$PWD
    cd build
    for PROJECT in `find $PWD -name .project`; do
        DIR=`dirname $PROJECT`
        echo $DIR
        cd $DIR
        awk -f $(rospack find mk)/eclipse.awk .project > .project_with_env && mv .project_with_env .project
    done
    cd $ROOT

# 修正Include path and symbols
change `__cplusplus=199711L` to `__cplusplus=201103L`