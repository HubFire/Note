# OpenCV的基础函数
*  读取、保存、显示
```C++
Mat image;
image = imread( imageName, IMREAD_COLOR );
imwrite( "../../images/Gray_Image.jpg", gray_image );
namedWindow( imageName, WINDOW_AUTOSIZE );
imshow( imageName, image );
```
* 颜色空间转换
```C++
cvtColor( image, gray_image, COLOR_BGR2GRAY );
```
