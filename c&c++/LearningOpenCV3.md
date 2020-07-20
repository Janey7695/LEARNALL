# Learning OpenCV3

## 1.Introduction to OpenCV
### 1.1 第一个程序-展示一张图片
~~~cpp
#include"opencv2/opencv.hpp"
#include<windows.h>
int main(int argc, char** argv) {
	cv::Mat img = cv::imread("background.gif", -1);
	if (img.empty())
		return -1;
	cv::namedWindow("example", cv::WINDOW_AUTOSIZE);
	cv::imshow("example", img);
	cv::waitKey(0);
	cv::destroyWindow("example");
	return 0;
}
~~~

OpenCV需要使用cv的namespace,因此要么提前申请`using namespace cv`，要么在使用opencv库函数时加上`cv::`。
>代码解析

1. `cv::Mat img = cv::imread("background.gif", -1);`使用这条命令来向程序load a image，`cv::imread()`是一个high-level routine的函数，它会根据文件名来自动确定文件格式（样式范围广，BMP, DIB, JPEG, JPE, PNG,PBM, PGM, PPM, SR, RAS, and TIFF），然后会自动为图像数据结构申请内存。
2. imread函数执行完毕后，会返回一个`cv::Mat`的数据结构，这个数据结构能处理多种文件类型，OpenCV uses this structure to handle all kinds of images: single-channel, multichannel, integer-valued，floating-point-valued, and so on.
3. `if (img.empty())`判断是否读取了图像文件
4. `cv::namedWindow("example", cv::WINDOW_AUTOSIZE);`是另一个high-level routine函数，它会创建一个窗口来包含和显示图片（该函数由HighGUI library提供）。第一个参数会给窗口命名。第二个参数定义了窗口属性，可以被设置为0（默认数值），或者cv::WINDOW_AUTOSIZE，前者无论图片大小，窗口都是固定的，会自动调整图片大小适应窗口，后者无论图片大小，窗口会自动调整适应图片大小。
5. `cv::imshow("example", img);`Whenever we have an image in a `cv::Mat` structure, we can display it in an existing window with `cv::imshow()`.The cv::imshow() function creates a window if one does not exist (created by cv::namedWindow()). On the call to cv::imshow(), the window will be redrawn with the appropriate image in it, and the window will resize itself as appropriate if it was created with the cv::WINDOW_AUTOSIZE flag.
6. `cv::waitKey(0);`当参数值为0或负数时，窗口会停止直到有keypress，当参数值为正数时，窗口会暂停相应的豪秒数，或者有keypress。
7. `cv::destroyWindow("example");`关闭窗口，释放内存。事实上，Mat数据结构会自动释放内存，因此小型程序不需要担心内存问题，但是当程序较大时，要记得关闭窗口，释放内存

### 1.2 第二个程序-展示一段影片
~~~cpp
#include"opencv2/highgui/highgui.hpp"
#include"opencv2/imgproc/imgproc.hpp"

int main(int argc, char** argv) {
	cv::namedWindow("video", cv::WINDOW_AUTOSIZE);
	cv::VideoCapture cap;
	cap.open("video.mp4");
	cv::Mat frame;
	for (;;)
	{
		cap >> frame;
		if (frame.empty())
			break;
		cv::imshow("video", frame);
		if (cv::waitKey(33) >= 0)
			break;
	}
	return 0;
}
~~~

众所周知，影视并不是真正的连续，是由很多帧画面，通过快速切换来达到动画的效果，这里的代码示例类似。
>代码解析

1. `cv::VideoCapture cap;`实例化了一个数据结构为VideoCapture的变量cap，它可以打开与关闭许多类型符合[ffmpeg]("https://baike.baidu.com/item/ffmpeg/2665727?fr=aladdin")规范的video file。
2. `cap.open("video.mp4");cv::Mat frame;`打开导入视频文件，并且实例化一个Mat数据结构的实例frame 来表示每一帧。
3. for (;;)
	{
		cap >> frame;
		if (frame.empty())
			break;
		cv::imshow("video", frame);
		if (cv::waitKey(33) >= 0)
			break;
	}
    从cap中读取每一帧，然后通过imshow来显示图像帧，用waitkey来进行延时，调整帧率（视频播放速度）。

`cv::waitKey()`,当keypress时，返回按键的ASCII码，若是填入大于0的参数进行计时暂停，暂停结束后返回<0的数。

### 1.3 Moving Around 可拖动进度条

~~~cpp
#include"opencv2/highgui/highgui.hpp"
#include"opencv2/imgproc/imgproc.hpp"
#include<iostream>
#include<fstream>

using namespace std;

int g_slider_position = 0;
int g_run = 1, g_dontset = 0;
cv::VideoCapture g_cap;

void onTrackbarSlider(int pos, void*) {
	g_cap.set(cv::CAP_PROP_POS_FRAMES, pos);
	if (!g_dontset)
		g_run = 1;
	g_dontset = 0;
}

int main(int argc, char** argv) {
	cv::namedWindow("video", cv::WINDOW_AUTOSIZE);
	g_cap.open("20200715_140751.mp4");
	int frames = (int)g_cap.get(cv::CAP_PROP_FRAME_COUNT);
	int tmpw = (int)g_cap.get(cv::CAP_PROP_FRAME_WIDTH);
	int tmph = (int)g_cap.get(cv::CAP_PROP_FRAME_HEIGHT);
	cout << "video has" << frames << "frames of dimensions(" << tmpw << "," << tmph << ")." << endl;
	cv::createTrackbar("position", "video", &g_slider_position, frames, onTrackbarSlider);
	cv::Mat frame;
	for (;;)
	{
		if (g_run!= 0)
		{
			g_cap >> frame;
			if (frame.empty())
				break;
			int current_pos = (int)g_cap.get(cv::CAP_PROP_POS_FRAMES);
			g_dontset = 1;
			cv::setTrackbarPos("position", "video", current_pos);
			cv::imshow("video", frame);
			g_run-= 1;
		}
		char c = (char)cv::waitKey(100);
		if (c == 's')
		{
			g_run = 1;
			cout << "Single step,run=" << g_run << endl;
		}
		if (c == 'r')
		{
			g_run = -1;
			cout << "Run mode,run=" << g_run << endl;
		}
		if (c == 27)
		{
			break;
		}
	}
	return 0;
}
~~~
为了使我们的代码可读性更好，在全局变量前面加上g_。

>代码分析
1. 
	int g_slider_position = 0;
	int g_run = 1, g_dontset = 0;
	cv::VideoCapture g_cap;
	首先我们定义了一些全局变量。g_slider_position来定义trackbar slider的位置状态。由于callback会使用修改到capture object g_cap，因此我们将这个类设定为全局变量。

2. void onTrackbarSlider(int pos, void*) {
	g_cap.set(cv::CAP_PROP_POS_FRAMES, pos);
	if (!g_dontset)
		g_run = 1;
	g_dontset = 0;
	}
	将鼠标在trackbar上拖动的位置的帧数pos作为参数，将g_cap的帧指针移动到相应的pos上。并且判断g_sontset,如果!g_sontset（即g_sontset=0）为真，则g_run设置为1，视频播放一帧后停下。

3. `cv::createTrackbar("position", "video", &g_slider_position, frames, onTrackbarSlider);`创建一个trackbar，第一个参数是trackbar的标签，名称。第二个参数决定它放置在哪个窗口。第四个参数表示trackbar能表示的最大帧数目。第五个函数可以放置一个callback函数来处理移动后的slider（不需要的话填NULL）。

4. if( g_run != 0 ) {
	g_cap >> frame; if(!frame.data) break;
	int current_pos = (int)g_cap.get(cv::CAP_PROP_POS_FRAMES);
	g_dontset = 1;
	cv::setTrackbarPos("Position", "Example2_4", current_pos);
	cv::imshow( "Example2_4", frame );
	g_run-=1;
	}
	这就是播放的核心步骤，与之前的代码相似。`(int)g_cap.get(cv::CAP_PROP_POS_FRAMES)`会获取当前播放到了第几帧，将`g_dontset`设置为1，防止下一次callback将视频状态设置为single step。然后调用callback函数去更新trackbar中slider展示给用户的位置。g_run进行递减，来保证程序在没有键盘输入的情况下工作在正常状态下。

5.  char c = (char) cv::waitKey(10);
	if( c == 's' ) // single step
	{g_run = 1; cout << "Single step, run = " << g_run << endl;}
	if( c == 'r' ) // run mode
	{g_run = -1; cout << "Run mode, run = " << g_run << endl;}
	if( c == 27 )
	break;
	通过键盘输入来对视频操作，s=移动一帧，r=播放视频,esc=退出视频.
	控制原理：当`g_run=1`时，由于`g_run-=1`,所以运行一次循环后，`g_run=0`,这样就不满足`g_run!= 0`，帧终止切换，即视频终止。当`g_run=-1`时，不论执行几次循环，都不可能=0，因此可以持续播放帧。

Note again, for short programs, we’ve omitted the step of cleaning up the window storage using cv::destroyWindow().

### 1.4 A Simple Transformation

对图片进行简单的处理，这里是一个模糊化的示例。
~~~cpp
#include"opencv2/opencv.hpp"

void example2_5(const cv::Mat& image) {
	cv::namedWindow("example_in", cv::WINDOW_AUTOSIZE);
	cv::namedWindow("example_out", cv::WINDOW_AUTOSIZE);

	cv::imshow("example_in", image);
	cv::Mat out;
	cv::GaussianBlur(image, out, cv::Size(5, 5), 3, 3);
	cv::GaussianBlur(out, out, cv::Size(5, 5), 3, 3);
	cv::imshow("example_out", out);
	cv::waitKey(0);
}


int main(int argc, char** argv) {
	cv::Mat in;
	in = cv::imread("background.gif", -1);
	if (in.empty())
		return -1;
	example2_5(in);
	return 0;
}
~~~

其中需要注意的是，使用`GaussianBlur`模糊时，Size(x,x)中的x必须为奇数。

### 1.5 A Not-So-Simple Transformation
多种不同的Transformation
#### 1.5.1 cv::pyrDowm(img1,img2)

功能：将img1宽，高都缩小一半后的图像赋给img2

~~~cpp
#include"opencv2/opencv.hpp"

int main(int argc, char** argv) {
	cv::Mat img1, img2;
	cv::namedWindow("example1", cv::WINDOW_AUTOSIZE);
	cv::namedWindow("example2", cv::WINDOW_AUTOSIZE);
	img1 = cv::imread("background.gif");
	cv::imshow("example1", img1);
	cv::pyrDown(img1, img2);
	cv::imshow("example2", img2);
	cv::waitKey(0);

	return 0;
}
~~~

#### 1.5.2 cv::cvtColor()与cv::Canny()

功能：`cv::cvtColor()`调整图像的颜色。`cv::Canny()`描边特效。

~~~cpp
#include"opencv2/opencv.hpp"

int main(int argc, char** argv) {
	cv::Mat img_rgb, img_gry, img_cny;
	cv::namedWindow("example gray", cv::WINDOW_AUTOSIZE);
	cv::namedWindow("example Canny", cv::WINDOW_AUTOSIZE);
	img_rgb = cv::imread("background.gif");
	cv::cvtColor(img_rgb, img_gry, cv::COLOR_BGR2GRAY);
	cv::imshow("example gray", img_gry);
	cv::Canny(img_gry, img_cny, 10, 100, 3, true);
	cv::imshow("example Canny", img_cny);
	cv::waitKey(0);
	return 0;
}
~~~

这边先将原图设置为灰色调，然后用Canny进行描边，先灰后描能够去除一些不必要的颜色干扰，提高描边准确性。

#### 1.5.3 随意组合之前的cv::pyrDowm(img1,img2)、cv::cvtColor()与cv::Canny()

~~~cpp
cv::cvtColor( img_rgb, img_gry, cv::BGR2GRAY );
cv::pyrDown( img_gry, img_pyr );
cv::pyrDown( img_pyr, img_pyr2 );
cv::Canny( img_pyr2, img_cny, 10, 100, 3, true );
.....//do what you want with img_cny
~~~

#### 1.5.4  Getting and setting pixels

~~~cpp
#include"opencv2/opencv.hpp"
#include<iostream>

int main(int argc, char** argv) {
	cv::Mat img_rgb, img_gry, img_cny, img_pyr, img_pyr2;
	int x = 16, y = 32;


	cv::namedWindow("example 1", cv::WINDOW_AUTOSIZE);
	cv::namedWindow("example 2", cv::WINDOW_AUTOSIZE);
	cv::namedWindow("example 3", cv::WINDOW_AUTOSIZE);
	cv::namedWindow("example 4", cv::WINDOW_AUTOSIZE);
	img_rgb = cv::imread("background.gif");

	cv::Vec3b intensity = img_rgb.at<cv::Vec3b>(y, x);
	uchar blue = intensity[0];
	uchar green = intensity[1];
	uchar red = intensity[2];//获取（x,y）点的色素信息

	cv::cvtColor(img_rgb, img_gry, cv::COLOR_BGR2GRAY);
	cv::pyrDown(img_gry, img_pyr);
	cv::pyrDown(img_pyr, img_pyr2);
	cv::Canny(img_pyr2, img_cny, 10, 100, 3, true);
	cv::imshow("example 1", img_rgb);
	cv::imshow("example 2", img_pyr2);
	cv::imshow("example 3", img_cny);
	std::cout << "At(x,y)=(" << x << "," << y << "):(blue,green,red)=(" << (unsigned int)blue << "," << (unsigned int)green << "," << (unsigned
		int)red << ")" << std::endl;

	std::cout << "Gray pixel there is:" << (unsigned int)img_gry.at<uchar>(y, x) << std::endl;

	x /= 4;y /= 4;
	std::cout << "Pyramid2 pixel there is :" << (unsigned int)img_pyr2.at<uchar>(y, x) << std::endl;
	img_cny.at<uchar>(x, y) = 128;

	cv::imshow("example 4", img_cny);
	cv::waitKey(0);

	return 0;
}
~~~
`cv::Vec3b`可以看作`Vector<uchar,3>`，是一个三字节的uchar容器，三个字节分别存储该像素点的颜色信息，用rgb系统来记录，即三基色记录法。
*2020/7/15 16：23*

### 1.6 Input from a Camera

通过摄像机获取视频信号

~~~cpp
#include"opencv2/opencv.hpp"
#include<iostream>
using namespace std;
int main(int argc, char** agrv)
{
	cv::VideoCapture cap;
	cv::namedWindow("example", cv::WINDOW_AUTOSIZE);

	if (argc == 1) {
		cap.open(0);
	}
	else {
		cap.open("20200715_140751.mp4");
	}
	if (!cap.isOpened()) {
		cerr << "Couldn't open capture." << endl;
		return -1;
	}
	cv::Mat frame;
	for (;;) {
		cap >> frame;
		if (frame.empty())
			break;
		cv::imshow("example", frame);
		if (cv::waitKey(100) >= 0)
			break;
	}
	return 0;
}
~~~

VideoCapture是个很万能的类，既可以导入disk上的视频，也可以导入camera上的实时视频信号，当`cap.open()`的参数是本地视频地址时，它会打开本地视频。当参数是0时，它会导入摄像头拍摄到的内容（默认是第一个与电脑连接的摄像头）。

### 1.7 Writing to an AVI File
这里以一个极坐标转化Log_Polar为例子
~~~cpp
#include"opencv2/opencv.hpp"
#include<iostream>

int main(int argc, char** agrv)
{
	cv::namedWindow("example", cv::WINDOW_AUTOSIZE);
	cv::namedWindow("log_polar", cv::WINDOW_AUTOSIZE);
	cv::VideoCapture capture(0);//导入摄像头视频

	double fps = capture.get(cv::CAP_PROP_FPS);//获取摄像头的帧率
	cv::Size size((int)capture.get(cv::CAP_PROP_FRAME_WIDTH), (int)capture.get(cv::CAP_PROP_FRAME_HEIGHT));//获取画面尺寸
	
	cv::VideoWriter writer;//实例化
	writer.open("example2.mp4", cv::CAP_OPENCV_MJPEG, fps, size);//新建一个名为example2.mp4的文件，编码格式是MJPEG，设置fps,size与摄像头视频相同
	cv::Mat logpolar_frame, bgr_frame;
	for (;;) {
		capture >> bgr_frame;
		if (bgr_frame.empty())
			break;
		cv::imshow("example", bgr_frame);

		cv::logPolar(bgr_frame, logpolar_frame, cv::Point2f(bgr_frame.cols / 2, bgr_frame.rows / 2), 40, cv::WARP_FILL_OUTLIERS);//极坐标化

		cv::imshow("log_polar", logpolar_frame);

		writer << logpolar_frame;//写处理后的帧到example2中
		char c = cv::waitKey(10);
		if (c == 27)
			break;
	}
	capture.release();//release后可以发现，cpp目录下多了一个example2的文件
	return 0;
}
~~~

### 1.8 总结 
通过这一章的一些例子，可以了解到OpenCV API的强大，简单了解了Mat,VideoCapture,VideoWriter以及它能提供的便利，能显示图片，修改图片，显示摄像头的实时画面...后面将通过进一步的学习来深入了解OpenCV提供的API。
*2020/7/16 10：59*

---

## 2. Getting to Know OpenCV Data Types

### 2.1 The Basics
在接下来的几章中，我们将介绍OpenCV的所有基本数据类型。用于处理数组（例如图像和大矩阵。此外我们也会学习到很多用于有效处理数据的大量函数。2.2到2.3节简单介绍数据类型，2.4节开始较深入了解各个类型。

### 2.2 OpenCV Data Types
OpenCV有许多被设计用来使处理计算机视觉相对容易和直观的数据类型，某些时候开发者可能需要一套相对强大的，可以自定义的操作工具，而OpenCV致力于满足他们，该库试图通过使用基本数据类型的模板来满足这两个需求，通过模板的专业化，使日常操作更加轻松。

OpenCV的数据类型大致可以分成三类：
1. 基本数据类型。直接由c++中继承得到(int,float,ect)，还包括一些简单的vector和matrices，以及一些简单的几何概念，如点，矩形，尺寸，形状...
2. 辅助对象(*helper objects*)。这些对象包括了更多抽象概念，如垃圾指针回收，范围对象切片，termination criteria等。
3. 大数组类型(*large array types*)。这个类型的一个star example就是Mat 类，通过大量的内部数组或任意元素来表示一张图片的信息。

除了上面的3类外，还需要注意的是OpenCV大量的使用了STL库，因此了解STL的话对于学习OpenCV有帮助。

### 2.3 Overview of the Basic Types

模板类`cv::Vec<>`，也被称为固定容器类(*fixed vector classes*). Why
not just use STL classes? The key difference is that the fixed vector classes are
intended for small vectors whose dimensions are known at compile time.

即使`cv::Vec<>`是一个模板类，但是你将更多的使用和见到它的别名(*typedefs*)，如`cv::Vec2i`(二维整型矢量)，`cv::Vec3i`(三维整型矢量),`cv::Vec4d`(四维双浮点(*double-precision float*)型矢量)。
总的来说`cv::Vec{2,3,4,6}{b,w,s,i,f,d}`的任意组合都是可行的，表示2到6维的6种数据类型。

除了fixed vector classes 外，还有fixed matrix classes，与模板类`cv::Matx<>`相关联。`cv::Matx<>`目的并非用于大数组操作，而是用于解决一些小矩阵问题，在计算机视觉中，有许多场景用到了2x2,3x3的矩阵，少量用到了4x4的矩阵。同样的`cv::Matx<>`也有别名，`cv::Matx{1,2,3,4,5,6}{1,2,3,4,5,6}{f,d}`.

与*fixed vector classes*紧密关联的是*point classes*，用来存储2到三个表示点位置的值。*point classes*与*fixed vector classes*最大的不同在于，它通过named variables来获取成员的值(mypoint.x,mypoint.y,ect),而*fixed vector classes*通过下标获取(myvec[0]),myvec[1],ect).它的别名有：cv::Point2i,cv::Point2f ...

类`cv::Scalar`本质上是一个四维的 point 。但是与point不同的是，Scalar获取数据通过下标，与vector相同，因为Scalar是由Vec<double,4>继承而来的。

`cv::Size和cv::Rect`，与*point classes*一样，它们由自己的模板类继承而来。 `cv::Size` is mainly distinguished by having data members width and height rather than x and y, while `cv::Rect` has all four. The class `cv::Size` is actually an alias for `cv::Size2i`.


### 2.4 Basic Types:Getting Down to Details
本节开始学习各个类型的用法，以及其提供的函数接口。源码在*opencv2/core/core.hpp*

#### 2.4.1 The point classes
在所有opencv基础类型中，*point  classes*应该是最简单的一个，正如我们前面提到的，它们基于一个模板结构执行，因此它们可以是points of any type(int float double-float ....)。它们有2个主要的模板类，一个是二维一个是三维point。它的优势主要体现在简单易用，开销少，虽然没有许多针对它们的操作，但是它们可以在必要时转化成其它类型的东西，比如*fixed classes*.
>Table 2.4.1 Function

|Operation|Example|
|---|---|
|Default constructors|`cv::Point2i p;` `cv::Point3f p;`|
|Copy constructors|`cv::Point3f p2(p1);`|
|Value constructors|`cv:Point2i p(x0,x1);` `cv:Point3d(x0,x1,x2);`|
|Cast to another classes|`(cv::Vec3f)p;`|
|Member access|p.x;p.y;(如果有第三维则是p.z)|
|Dot product(数量积、点乘)|`float x=p1.dot(p2);`|
|Double-precision dot product |`double x=p1.ddot(p2);`|
|Cross product(叉乘)|`p1.cross(p2)`//仅限三维|
|Query if point p is inside rectangle r(查询p是否在r内)|`p.inside(r)`//仅限二维|

#### 2.4.2 The cv::Salar class
`cv::Salar`：是一个四维的point class，跟其它类一样与一个模板类相关联，但是那些访问它的别名回返回的实例化的模板的成员均为双精度浮点数。

>Table 2.4.2 Function

|Operation|Example|
|---|---|
|Default constructors|`cv::Scalar s;`|
|Copy constructors|`cv::Scalar s2(s1);`|
|Value constructors|`cv::Scalar s(x0);` `cv::Scalar s(x0,x1,x2,x3);`|
|Element-wise multiplication[元素智能乘积]("https://www.zhihu.com/question/65866370")|`s1.mul(s2);`|
|(Quaternion) conjugation(四元素共轭??)|`s.conj();`//return cv::Scalar(s0,-s1,-s2,-s3)???|
|(Quaternion) real test|`s.isReal();`//return true if s1==s2==s3==0???|

*上面的x0是实数，x1,x2,x3是复数

*与教程不同的是，在另一个视频中，Scalar好像是用来代表颜色的(bgr表达方式)

*表格最后2个还不懂是干嘛的，代码测试结果也跟想的不一样*

*2020/7/17 10:04*
#### 2.4.3 The Size classes 
size classes 与 point classes类似，并且可以cast to and from them。主要的区别是，point 的数据成员是`x`,`y`，而size 的成员是 `width` 和 `height`. size有三个别名，分别是`cv::Size`,`cv::Size2i`,`cv::Size2f`(32位的浮点类型).

>Table 2.4.3 Function

|Operation|Example|
|---|---|
|Default constructors|cv::Size sz;cv:: Size2i sz;cv::Szie2f sz;|
|Copy constructors|cv::Size sz2(sz1);|
|Value constructors|cv::Size2f sz(w,h);|
|Member access|sz.width;sz.heigth|
|Compute area|sz.area();|

但是需要注意的还有：与point classes 不同，size不能cast to fixed vector classes 这意味着size还是有一些作用限制的。而从另一方面来说，point classes和fixed vector 可以完全没问题的cast to size classes。

#### 2.4.4 The cv::Rect class
这是一个矩形类，包含了一个point class的x,y，用来表示矩形的左上角坐标以及一个size class的width 和 heigth来表示矩形的长宽。并且需要注意的是，这个矩形类并不是从point 或者 size类里继承而来的，因此它也没有继承point 或者 size 的操作API。

>Table 2.4.4.a Function

|Opreation|Example|
|---|---|
|Default constructors|cv::Rect r;|
|Copy constructors|cv::Rect r2(r1);|
|Value constructors|cv::Rect(x,y,w,h);|
|construct from origin and size|cv::Rect(p,sz);|
|construct from two corners(通过2个点来画一个矩形)|cv::Rect(p1,p2);|
|Member access|r.x;r.y;r.width;r.heigth;|
|Compute area|r.area();|
|Extract upper-left corner(获取左上角坐标)|r.tl();|
|Extract buttom-right corner|r.br();|
|Determine if point *p* is inside rectangle *r*|r.contains(p);|

除了这些基本操作外，`cv::Rect`还提供了许多重载函数来对计算2个矩形或矩形与其它图像的几何特性
>Table 2.4.4.b Function

|Opreation|Example|
|---|---|
|Intersection of rectangles *r1* and *r2*|cv::Rect r3=r1&r2;r1&=r2;|
|Minimum area rectangle containing rectangle *r1* and *r2*(同时包含了r1成分与r2成分的最小共有矩形)|cv::Rect r3=r1|r2;r1|=r2;|
|Translate rectangle *r* by an amount x|cv::Rect rx=r+x;r+=x;|
|Enlarge a rectangle *r* by an amount given by size *s*|cv::Rect rs=r+s;r+=s;|
|Compare rectangle *r1* and *r2* for exact equality|bool eq=(r1==r2);|
|Compare rectangle *r1* and *r2* for inequality|bool eq=(r1!=r2);|

***表格除了第二个 都还不明白用处是什么***


#### 2.4.5 The cv::RotatedRect class
旋转矩形类是c++ OpenCV中为数不多的非模板类，相反，它仅仅是一个容器包含了称为*center*的`cv::Point2f`和称为*size*的`cv::Size2f`以及一个额外的浮点数`float`称为*angle*。angle表示了矩形整体的旋转角度。RotatedRect与Rect最大的区别在于，RotatedRect的逻辑中心点在center，而Rect的逻辑中心点在左上角，并且RotatedRect可以旋转矩形，构成斜的矩形，而Rect只能做出老老实实的矩形。

>Table 2.4.5 Function

|Opreation|Example|
|---|---|
|Default constructors|cv::RotatedRect rr();|
|Copy constructor|cv::RotatedRect rr2(rr1);|
|Construct from two corners|cv::RotatedRect(p1,p2);|
|Value constructors;takes a point ,a size,and a angle|cv::RotatedRect rr(p,sz,theta);|
|Member access|rr.center;rr.size;rr.angle;|
|Return a list of the corners|rr.points(pts[4]);|

#### 2.4.6 The fixed matrix classes
fixed matrix之所以叫fixed matrix 是因为这样的矩阵在编译的时候，就已经知道矩阵的大小了，所使用的内存被申请与stack中，因此可以被快速清除，并且针对它的操作也非常快速。

>Table 2.4.6 Function

|Operation|Example|
|---|---|
|Default constructor|cv::Matx33f m33f;cv::Matx 43d m43d;|
|copy constructor|cv::Matx22d m22d(n22d);|
|Value constructors|cv::Matx21f m(x0,x1);cv::Matx44d m(x0,x1,x2,x3,x4,x5,x6,x7,x8,x9,x10,x11,x12,x13,x14,x15);|
|Matrix of identical elements|m33f=cv::Matx33f::all(x);|
|Matrix of zeros(初始化为0矩阵)|m23d=cv::Matx23d::zeros();|
|Matrix of ones(初始化为单位矩阵)|m16f=cv::Matx16f::ones();|
|Create a unit matrix|m33f=cv::Matx33f::eye();|
|Create a matrix that can hold the diagonal of another|m31f=cv::Matx33f::diag();|
|Create a matrix with uniformly distributed entries|m33f=cv::Matx33f::randu(min,max);|
|Create a matrix with normally distributed entries(创建具有正态分布条目的矩阵)|m33f=cv::Matx33f::urandu(mean,variance);|
|Member access|m(i,j),m(i);|
|Matrix algebra|m1=m0;m1*m2;m1-m0;|
|Singleton algebra|m\*a;a*m;m/a;|
|Comparison|m1==m2;m1!=m2;|
|Dot product|m1.dot(m2);m1.ddot(m2);|
|Reshape a matrix|m19f=m33f.reshape<9,1>();|
|Cast operators|m44f=(Matx44f)m44d|
|Extract 2x2 submatrix at(*i,j*)|m44f.get_minor<2,2>(i,j);|
|||
|||
|||
|||
|||
|||
|||
|||
|||
