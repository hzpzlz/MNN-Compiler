# MNN-Compile
## MNN编译采坑流程
### 1 训练模型得到pth，pth转成onnx
### 2 onnx转换为MNN框架所需的mnn格式
### 3 下载MNN，解压，编译MNN
### 4 首先写在掉原来linux系统中的libprotobuf-dev protobuf-compiler、pip uninstall protobuf
    sudo apt-get autoremove libprotobuf-dev protobuf-compiler
### 5 下载protocbuf源码：https://github.com/protocolbuffers/protobuf/releases/tag/v3.15.6 下Source code(tar.gz)
### 6 执行代码产生configure文件
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
### 7 查看版本：3.15.6
    protoc --version
### 8 修改CMakeLists.txt文件
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
### 9 编译流程
    cd MNN
    ./schema/generate.sh
    mkdir
    build
    cd build
    cmake .. -DMNN_BUILD_CONVERTER=true && make -j4
- 会在MNN/build目录下生成MNNConvert可执行文件  (修改了以后要在ncnn目录下的CMakeLists中添加include_directories(/usr/local/protobuf/include)
 
    
