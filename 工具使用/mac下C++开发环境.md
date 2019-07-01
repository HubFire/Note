## mac下C++环境搭建：Cmake,Vscode,Gcc
* 安装Xcode
    * 从APP store中安装Xcode
    * 安装Command Line Tools，执行xcode-select --install 
* 安装VScode
    * 下载并安装VScode
    * VS code安装C++插件  C/C++ , C++ intellisence , C++ Clang ,Cmake
* 安装CMake
    * 下载并安装Cmake，https://cmake.org/download/
    * 在系统PATH中增加 export PATH="/Applications/CMake.app/Contents/bin":"$PATH" <br>
##### 此时Mac 下C++开发环境已经安装好了
* 命令行下验证各软件：
    * cmake --version
    * make -v
    * gcc/g++ -v
    * lldb -v
    
    
