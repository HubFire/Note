## Cmake使用OpenCV
下面以一个简单的显示图片的程序演示如何在Cmake中使用OpenCV <br>
编写源程序
```
#include <stdio.h>
#include <opencv2/opencv.hpp>
using namespace cv;
int main(int argc, char** argv )
{
    if ( argc != 2 )
    {
        printf("usage: DisplayImage.out <Image_Path>\n");
        return -1;
    }
    Mat image;
    image = imread( argv[1], 1 );
    if ( !image.data )
    {
        printf("No image data \n");
        return -1;
    }
    namedWindow("Display Image", WINDOW_AUTOSIZE );
    imshow("Display Image", image);
    waitKey(0);
    return 0;
}
```
Cmake 文件，新建CMakeList
```C++
cmake_minimum_required(VERSION 2.8)
SET(PROJECT_NAME DisplayImage)
PROJECT(${PROJECT_NAME})
#project( DisplayImage )
# find_package( OpenCV 3.4.6 REQUIRED )
find_package( OpenCV REQUIRED )
include_directories( ${OpenCV_INCLUDE_DIRS} )
add_executable( DisplayImage DisplayImage.cpp )
target_link_libraries( DisplayImage ${OpenCV_LIBS} )
```
然后就可以执行cmake，make编译程序了
