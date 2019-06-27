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
