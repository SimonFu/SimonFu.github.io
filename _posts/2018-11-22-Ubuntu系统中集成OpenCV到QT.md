在Linux系统中，QT是一个很不错的C++的框架，并提供了IDE。很多想学习OpenCV的朋友都想把OpenCV集成到QT中，本文就简单介绍一下怎样在QT中集成OpenCV。  
###编译安装OpenCV   
参考[官方的教程](https://docs.opencv.org/2.4/doc/tutorials/introduction/linux_install/linux_install.html?highlight=installation) ，这里就不多说了。  
###编写QT测试程序
创建Console程序，在Project文件中加入如下配置：
```c
INCLUDEPATH += /usr/local/include/opencv4
DEPENDPATH += /usr/local/include/opencv4

LIBS += -L/usr/local/lib/   \
                -lopencv_calib3d    \
                -lopencv_core    \
                -lopencv_dnn    \
                -lopencv_features2d    \
                -lopencv_flann    \
                -lopencv_gapi    \
                -lopencv_highgui    \
                -lopencv_imgcodecs    \
                -lopencv_imgproc    \
                -lopencv_ml    \
                -lopencv_objdetect    \
                -lopencv_photo    \
                -lopencv_stitching    \
                -lopencv_videoio    \
                -lopencv_video
```
请注意我的OpenCV的安装目录是/usr/local，如果你安装在其他路径上，请把INCLUDEPATH和DEPENDPATH变量修改为你的安装路径。
在main.cpp文件中包含OpenCV的头文件，和使用cv命名空间。
```cpp
#include "opencv2/opencv.hpp"

using namespace cv;
```  
在main函数中加入如下代码：
```cpp
    Mat img = cv::imread(argv[1]); //加载命令行指定的图片
    if(img.empty())
    {
        return 0;
    }

    imshow("实例图片", img);
```   
如果一切正常将会输出如下图片。
![Hell world](../data/image/hello_opencv.png  "Hello OpenCV")  
注意QT有自己的消息循环，不需要用轮训等待的方式显示图片。