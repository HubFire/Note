OpenCV 官方文档：https://docs.opencv.org/3.4.6/d1/dfb
## OpenCV主要的模块架构如下
- core :核心模块，包含基本的数据结构，如多维矩阵，以及其他模块用到的基础函数
- imgproc: 图像处理相关的，包含线性、非线性滤波，几何变换(缩放、仿射、透视、重投影),颜色空间变换，直方图等。
- video: 视频分析模块，包括运动估计、背景消除、对象跟踪等算法
- calib3d: 相机标定和三维重建相关的模块，包含多视图几何算法,单目/立体相机标定，物体姿态估计,立体相机匹配算法，３Ｄ重建
- features2d: 特征检测,特征描述子，特征匹配
- objdetect: 目标检测算法以及预定义的实体类
- highgui: 易于使用的ＵＩ界面
- videoio: 视频捕获和视频解码
- ... 其他帮助模块，FLANN，google 测试模块，python 绑定等

## API相关概念
### cv Namespace
所有的opencv类和函数都在cv的命名空间下,因此使用的时候需要包含cv的命名空间
```C++
#include "opencv2/core.hpp"
cv::Mat H = cv::findHomography(points1, points2, cv::RANSAC, 5);
```
或者:
```C++
#include "opencv2/core.hpp"
using namespace cv;
Mat H = findHomography(points1, points2, RANSAC, 5 );
```
opencv中有些命名可能会与STL或其他库的命名冲突，因此在使用它们的时候显示的指定命名空间来解决冲突
```
Mat a(100, 100, CV_32F);
randu(a, Scalar::all(1), Scalar::all(std::rand()));
cv::log(a, a);
a /= std::log(2.);
```
### Automatic Memory Management
OpenCV自动管理内存
* std::vector, cv::Mat以及其他用到的数据结构都有析构函数，在必要的时候会自动释放内存。同时，OpenCV 也考虑到数据共享，每个数据缓冲区都有对应的引用计数器，复制的时候引用计数器增加，并没有实际的拷贝数据区。只有当引用计数器减到０时才会释放内存。
* 如果需要深拷贝数据，Mat提供了Clone函数
```C++
// 创建一个８Ｍb的大矩阵
Mat A(1000, 1000, CV_64F);
// 为该矩阵创建一个新的引用
// 该操作是即时的，不会分配新的内存，之前分配的内存引用计数会加１
Mat B = A;
// 为矩阵的第３行创建新的引用，依然没有数据拷贝
Mat C = B.row(3);
// 创建该矩阵的独立副本
Mat D = B.clone();
// 将Ｂ的第五行复制到Ｃ，相当于Ａ的第５行复制到Ａ的第三行
B.row(5).copyTo(C);
// 将Ａ指向新的矩阵，Ａ和Ｄ的数据是新分配的未更改矩阵，Ｂ和Ｃ依然指向之前Ａ修改过的矩阵
A = D;
// 使Ｂ成为空矩阵，实际上只是解除Ｂ的指向内存，因为Ｃ还在引用原来的内存区域，所以存储还不会释放
B.release();
// 将Ｃ的引用指向新的独立复制的矩阵，此时将释放最开始修改过的A的内存区域，因为它的引用计数为0
C = C.clone();
```
* 对于Mat或其他的简单数据结构可以使用上边的简单方法管理内存，对于高级类甚至用户自定义的类就需要使用智能指针。 OpenCVC提供智能指针模板类cv::Ptr 类似于C++ 11 里的std::shared_ptr，使用方法如下：
```C++
// 普通的指针
T* ptr = new T(...);
// 智能指针，封装了一个指向T类型实例的指针和一个引用计数器
Ptr<T> ptr(new T(...));  或  Ptr<T> ptr = makePtr<T>(...);
```
## Automatic Allocation of the Output Data
*  OpenCV不仅自动释放内存，而且在大多数时候自动为函数的输出参数分配内存空间。如果一个函数有一个或多个输入Mat，有输出的矩阵的时候，输出数组的空间会被自动分配，大小和数据类型由输入的类型决定
```C++
#include "opencv2/imgproc.hpp"
#include "opencv2/highgui.hpp"
using namespace cv;
int main(int, char**)
{
    VideoCapture cap(0);
    if(!cap.isOpened()) return -1;
    Mat frame, edges;
    namedWindow("edges", WINDOW_AUTOSIZE);
    for(;;)
    {
        cap >> frame;
        cvtColor(frame, edges, COLOR_BGR2GRAY);
        GaussianBlur(edges, edges, Size(7,7), 1.5, 1.5);
        Canny(edges, edges, 0, 30, 3);
        imshow("edges", edges);
        if(waitKey(30) >= 0) break;
    }
    return 0;
}
```
上面程序中frame的空间由操作符>> 分配，cap知道视频帧的分辨率以及通道数。edges的内存由cvtColor函数分配，分辨率与输入的frame一样，COLOR_BGR2GRAY决定了edges为灰度图，通道为1。frame和edges的内存在循环的第一次进行分配，之后都是同一内存区域。只有当视频的分辨率改变时候才会释放内存进行重新分配。
实现该功能主要是大多数函数都调用了Mat::create函数对输出数组进行内存分配。少数的函数例外，需要额外的处理（如 cv::mixChannels, cv::RNG::fill）


