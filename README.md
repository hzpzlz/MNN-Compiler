# MNN-Compile
## MNN编译采坑流程--Linux
## 环境要求
- cmake（建议使用3.10或以上版本）
- protobuf（使用3.0或以上版本）
- gcc（使用4.9或以上版本）
## 一 重装protobuf
### 1 首先卸载掉原来linux系统中的libprotobuf-dev protobuf-compiler、pip uninstall protobuf
    sudo apt-get autoremove libprotobuf-dev protobuf-compiler
### 2 下载protocbuf源码：https://github.com/protocolbuffers/protobuf/releases/tag/v3.15.6 
### 下载Source code(tar.gz)
### 3 执行代码产生configure文件
    ./autogen.sh
- 指定安装路径
```
./configure --prefix=/usr/local/protobuf
make
sudo make install
```
- 配置环境变量
```
vi /etc/profile
```
- 在最后一行添加：
```
export PATH=$PATH:/usr/local/protobuf/bin/
export PKG_CONFIG_PATH=/usr/local/protobuf/lib/pkgconfig/
```
- 配置动态库
```
sudo vim /etc/ld.so.conf
```
- 添加：
```     
/usr/local/protobuf/lib
```
- 执行
```
sudo ldconfig
```
### 4 查看版本：3.15.6
    protoc --version
    
## 二 编译MNN
### 1 下载MNN，https://github.com/alibaba/MNN，解压后重命名为MNN
### 2 修改MNN/CMakeLists.txt文件
- 在include_directories中，第298行添加 
```
/usr/local/protobuf/include
```
- 拷贝缺失的文件
- /usr/lib/x86_64-linux-gnu目录下执行
```
sudo cp /usr/local/protobuf/lib/libprotobuf.so ./
```
- /usr/bin目录下执行
```
cp /usr/local/protobuf/bin/protoc ./
```
### 3 编译流程
    cd MNN
    ./schema/generate.sh
    mkdir
    build
    cd build
    cmake .. -DMNN_BUILD_CONVERTER=true && make -j4
- 会在MNN/build目录下生成MNNConvert可执行文件  (修改了以后要在ncnn目录下的CMakeLists中添加include_directories(/usr/local/protobuf/include)
 
## 参考链接
- https://www.yuque.com/mnn/en/cvrt_linux
- https://blog.csdn.net/weixin_42388228/article/details/106056740?utm_medium=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.control&dist_request_id=1328680.7344.16161238227677931&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.control
- https://blog.csdn.net/guotong1988/article/details/88954358
- https://blog.csdn.net/weixin_39446424/article/details/104962765
