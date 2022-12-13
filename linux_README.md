# 编译OpenSceneGraph的注意事项

https://github.com/openscenegraph/OpenSceneGraph

## 先修改源
```
sudo nano /etc/apt/sources.list
```
打开所有的deb-src行，允许下载依赖包

```
sudo apt-get update
sudo apt install cmake
sudo apt-get install -y build-essential  （否则Cmake报错 No CMAKE_CXX_COMPILER could be found）
```

## 安装OSG所有的依赖包
```
sudo apt-get build-dep openscenegraph
```
如果还不行，下面的手工安装：
```
sudo apt-get install libgl1-mesa-dev
sudo apt-get install libfreetype6-dev
sudo apt-get install libjpeg-dev
```
可能会提示缺少依赖包，不是必选
```
CMake Error: The following variables are used in this project, but they are set to NOTFOUND.
```

后续按照原Readme进行osg的编译。编译完毕的osg的lib按照原说明拷贝到本地lib目录中。



# 解决linux下的gdal问题


## 构建机的ubuntu18.04上安装gdal
```
sudo add-apt-repository -y ppa:ubuntugis/ppa
sudo apt update
sudo apt upgrade
apt install gdal-bin python-gdal python3-gdal libgdal-dev

$ gdalinfo --version
GDAL 2.4.2, released 2019/06/28
说明安装成功
```


## 打开宏开关屏蔽的gdal相关代码
参考：https://github.com/fanvanzh/3dtiles/issues/219
'''
tileset.cpp

#ifdef _WIN32 开关屏蔽的epsg_convert wkt_convert 函数放开实现

'''


## 删除src/gdal文件夹
编译依赖头文件将来自于 /usr/include/gdal

## GDAL_DATA目录改为从os环境变量设置更为方便。windows版是获取exe所在目录并自动设置。
'''
epsg_convert wkt_convert 函数

    // 改为从os环境变量获取
    // CPLSetConfigOption("GDAL_DATA", path);
'''

## 增加gdal构建配置
build.rs的linux编译配置中增加：    println!("cargo:rustc-link-lib=gdal");


## main.rs去除一个编译警告。
'''
#[allow(dead_code)]
'''

## cargo build应该可以了。

# 动态链接库依赖
gdal依赖的库也不少。如果要拷贝到其他位置运行，查看其所有依赖
'''
ldd _3dtile
'''
拷贝所有依赖（含软连接）到执行环境，并设置LD_LIBRARY_PATH环境变量。


