本文介绍两种方法来把OpenCV的Mat转换成QT的QImage或者QPixmap。
### 方法一  
本方法基本思路是把图片编码成某种格式图片的Buffer，然后QT或者OpenCV框架用该Buffer来构造出图片。
#### cv::Mat转成QImage和QPixmap
```cpp
QImage matToImage(const Mat& cvImage) {
    vector<uchar> imgBuf;
    imencode(".bmp", cvImage, imgBuf);

    QByteArray baImg((char*)imgBuf.data(), static_cast<int>(imgBuf.size()));
    QImage qimg;
    qimg.loadFromData(baImg, "BMP");
    return qimg;
}

QPixmap matToPixmap(const Mat& cvImage)
{
    return QPixmap::fromImage(matToImage(cvImage));
}
```  
#### QImage和QPixmap转换cv::Mat
```cpp
Mat imageToMat(const QImage& image) {
    QByteArray ba;
    QBuffer buffer(&ba);
    buffer.open(QIODevice::WriteOnly);
    image.save(&buffer, "BMP");
    vector<uchar> imgBuf;
    return imdecode(std::vector<uchar>(ba.begin(), ba.end()), IMREAD_COLOR);
}

Mat PixmapToMat(const QPixmap& image) {
    return imageToMat(image.toImage());
}
```  
我把图转换成BMP格式而没有转换成JPG、PNG格式，主要考虑的是性能因素。编码成JPG和PNG格式，编码器需要更多的CPU计算才可以完成，但是消耗少量的内存；与之相反，转换成BMP消耗更多的内存，而节省大量的CPU计算。请根据你自己的约束选择正确的策略。  
### 方法二
在github上有一个项目asmOpenCV[asmOpenCV](https://github.com/asmaloney/asmOpenCV) ，这个项目提供API来完成转换。所有代码都在一个头文件中，非常容易集成。这个方法比较简单就不多介绍了。  
### 比较   

#### 方法一   
##### 优点   
1. 该方案不依赖任何第三方的代码。
2. 该方案适应性比较好，能处理各种格式的图片。
##### 缺点   
1. 该方案转换过程中需要编解码过程，性能会受到影响。
2. 该方案需要Buffer来保存临时生成的图片，需要消耗更多的内存。  

#### 方法二   
##### 优点   
1. 生成QImage和QPixmap不需要重新申请内存，直接使用Mat的内存，效率比较高，并且节省内存。
2. 所有代码只在一个头文件中，比较容易集成。
##### 缺点   
1. 支持部分图片格式，适应性不好。