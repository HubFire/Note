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
## Saturation Arithmetics
OpenCV中图像的像素通常是8位或16位编码的，因此能取得值有限。但是在做某些处理（如色彩空间转换、亮度/对比度调整、锐化、复杂插值(双立方、Lanczos)）可能会生成范围外的值，如果只存储结果的最低 8 (16) 位,则会导致视觉伪影,并可能影响进一步的图像分析。此时OpenCV会将操作结果r用0～255之件最接近r的值替代：
```C++
I(x,y)=min(max(round(r),0),255)
```
与此类似的，8位有符号，16位有符号或无符号也会做saturate处理（32位的integer不会saturate）
```C++
I.at<uchar>(y, x) = saturate_cast<uchar>(r);
```
## Fixed Pixel Types. Limited Use of Templates
有限的使用模板：目前的OpenCV基于模板实现运行时的多态。但是由于以下原因，使用的较少：
* 大量使用模板可能会增加编译时间和代码大小
* 使用模板时无法分离接口和实现,对于单个算法有几千行代码的视觉库不好
* 其他语言（Python Java Matlab）不支持模板或者支持有限

有限的数据类型，数组元素需要是下列类型之一：
* 8-bit unsigned integer (uchar)
* 8-bit signed integer (schar)
* 16-bit unsigned integer (ushort)
* 16-bit signed integer (short)
* 32-bit signed integer (int)
* 32-bit floating-point number (float)
* 64-bit floating-point number (double)
OpenCV 数组里的所有元素需要是同一类型，目前最多支持的通道数为512，由常量CV_CN_MAX定义
基本的数据类型枚举
```C++
enum { CV_8U=0, CV_8S=1, CV_16U=2, CV_16S=3, CV_32S=4, CV_32F=5, CV_64F=6 };
```
CV_8UC1 ... CV_64FC4 通道数常量（通道数从1到4）
CV_8UC(n) ... CV_64FC(n) or CV_MAKETYPE(CV_8U, n) ... CV_MAKETYPE(CV_64F, n)超过4或者在运行时不知道时候
例如：
```C++
Mat mtx(3, 3, CV_32F); // 3*3的浮点数矩阵
Mat cmtx(10, 1, CV_64FC2); // 10*1的Double数组，两通道
Mat img(Size(1920, 1080), CV_8UC3); // 1080行，1920列3通道无符号8位
Mat grayscale(image.size(), CV_MAKETYPE(image.depth(), 1)); // 生成1通道，和image大小一样的矩阵                                                       
```
* 人脸检测算法只适用于8位灰度图和彩图
* 线性代数函数和大多数机器学习函数都是浮点数
* 基本的函数，比如cv::add支持所有的类型
* 颜色空间转换支持8位无符号，16位无符号以及32位浮点
##  InputArray/OutputArray
https://www.cnblogs.com/freshmen/p/4534966.html
InputArray/OutputArray是两个接口类，这个类只能作为函数的形参参数使用，不要试图声明一个InputArray类型的变量。InputArray这个接口类可以是Mat、Mat_<T>、Mat_<T, m, n>、vector<T>、vector<vector<T>>、vector<Mat>。如果在你自己编写的函数中形参也想用InputArray，可以传递多类型的参数，在函数的内部可以使用_InputArray：：getMat（）函数将传入的参数转换为Mat的结构，方便你函数内的操作；必要的时候，可能还需要_InputArray：：kind（）用来区分Mat结构或者vector<>结构，但通常是不需要的。例如：
```C++
    void myAffineTransform(InputArray _src, OutputArray _dst, InputArray _m)  
{  
  
    Mat src = _src.getMat(), m = _m.getMat();  
    CV_Assert( src.type() == CV_32FC2 && m.type() == CV_32F && m.size() == Size(3, 2) );  
    _dst.create(src.size(), src.type());  
    Mat dst = _dst.getMat();  
    for( int i = 0; i < src.rows; i++ )  
        for( int j = 0; j < src.cols; j++ )  
        {  
            Point2f pt = src.at<Point2f>(i, j);  
            dst.at<Point2f>(i, j) = Point2f(m.at<float>(0, 0) * pt.x +  m.at<float>(0, 1) *   pt.y + m.at<float>(0, 2);  
        }  
}
```
    
