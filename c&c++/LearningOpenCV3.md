
# Learning OpenCV3 <div id="1">

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
>导航

2.1 <a href="#2.1">The Basics</a><br>
2.2 <a href="#2.2">OpenCV Data Types</a><br>
2.3 <a href="#2.3">Overview of the Basic Types</a><br>
2.4 <a href="#2.4">Basic Types:Getting Down to Details</a><br>
——2.4.1 <a href="#2.4.1">The point classes</a><br>
——2.4.2 <a href="#2.4.2">The cv::Salar class</a><br>
——2.4.3 <a href="#2.4.3">The Size classes</a><br>
——2.4.4 <a href="#2.4.4">The cv::Rect class</a><br>
——2.4.5 <a href="#2.4.5">The cv::RotatedRect class</a><br>
——2.4.6 <a href="#2.4.6">The fixed matrix classes</a><br>
——2.4.7 <a href="#2.4.7">The fixed vector classes</a><br>
——2.4.8 <a href="#2.4.8">The complex number classes</a><br>

2.5 <a href="#2.5">Helper Object</a><br>
——2.5.1 <a href="#2.5.1">The cv::TermCriteria class</a><br>
——2.5.2 <a href="#2.5.2">The cv::Range class</a><br>
——2.5.3 <a href="#2.5.3">The cv::Ptr template and Garbage Collection 101</a><br>
——2.5.4 <a href="#2.5.4">The cv::Exception class and exception handling</a><br>
——2.5.5 <a href="#2.5.5">The cv::DataType<>template</a><br>
——2.5.6 <a href="#2.5.6">The cv::InputArray and cv::OutputArray classes</a><br>

2.6 <a href="#2.6">Utility Functions</a><br>
——2.6.1 <a href="#2.6.1">cv::alignPtr</a><br>
——2.6.2 <a href="#2.6.2">cv::alignSize()</a><br>
——2.6.3 <a href="#2.6.3">cv::allocate()</a><br>
——2.6.4 <a href="#2.6.4">cv::deallocate()</a><br>
——2.6.5 <a href="#2.6.5">cv::fastAtan2()</a><br>
——2.6.6 <a href="#2.6.6">cvCeil()</a><br>
——2.6.7 <a href="#2.6.7">cvFloor()</a><br>
——2.6.8 <a href="#2.6.8">cv::cubeRoot()</a><br>
——2.6.9 <a href="#2.6.9">cv::CV_Assert()</a><br>
——2.6.10 <a href="#2.6.10">CV_Error() and CV_Error_()</a><br>
——2.6.11 <a href="#2.6.11">cv::error()</a><br>
——2.6.12 <a href="#2.6.12">cv::fastFree() and cv::fastMalloc()</a><br>
——2.6.13 <a href="#2.6.13">cv::format()</a><br>
——2.6.14 <a href="#2.6.14">cv::getCPUTickCount()</a><br>
——2.6.15 <a href="#2.6.15">cv::getNumThreads()</a><br>
——2.6.16 <a href="#2.6.16">cv::getOptimalDFTSize()</a><br>
——2.6.17 <a href="#2.6.17">cv::getThreadNum()</a><br>
——2.6.18 <a href="#2.6.18">cv::getTickCount()</a><br>
——2.6.19 <a href="#2.6.19">cv::getTickFrequency()</a><br>
——2.6.20 <a href="#2.6.20">cvIsInf()</a><br>
——2.6.21 <a href="#2.6.21">cvIsNaN()</a><br>
——2.6.22 <a href="#2.6.22">cvRound()</a><br>
——2.6.23 <a href="#2.6.23">cv::setNumThreads()</a><br>
——2.6.24 <a href="#2.6.24">cv::setUseOptimized()</a><br>
——2.6.25 <a href="#2.6.25">cv::useOptimized()</a><br>



### 2.1 The Basics <h id="2.1">
在接下来的几章中，我们将介绍OpenCV的所有基本数据类型。用于处理数组（例如图像和大矩阵。此外我们也会学习到很多用于有效处理数据的大量函数。2.2到2.3节简单介绍数据类型，2.4节开始较深入了解各个类型。

### 2.2 OpenCV Data Types <h id="2.2">
OpenCV有许多被设计用来使处理计算机视觉相对容易和直观的数据类型，某些时候开发者可能需要一套相对强大的，可以自定义的操作工具，而OpenCV致力于满足他们，该库试图通过使用基本数据类型的模板来满足这两个需求，通过模板的专业化，使日常操作更加轻松。

OpenCV的数据类型大致可以分成三类：
1. 基本数据类型。直接由c++中继承得到(int,float,ect)，还包括一些简单的vector和matrices，以及一些简单的几何概念，如点，矩形，尺寸，形状...
2. 辅助对象(*helper objects*)。这些对象包括了更多抽象概念，如垃圾指针回收，范围对象切片，termination criteria等。
3. 大数组类型(*large array types*)。这个类型的一个star example就是Mat 类，通过大量的内部数组或任意元素来表示一张图片的信息。

除了上面的3类外，还需要注意的是OpenCV大量的使用了STL库，因此了解STL的话对于学习OpenCV有帮助。

### 2.3 Overview of the Basic Types <h id="2.3">

模板类`cv::Vec<>`，也被称为固定容器类(*fixed vector classes*). Why
not just use STL classes? The key difference is that the fixed vector classes are
intended for small vectors whose dimensions are known at compile time.

即使`cv::Vec<>`是一个模板类，但是你将更多的使用和见到它的别名(*typedefs*)，如`cv::Vec2i`(二维整型矢量)，`cv::Vec3i`(三维整型矢量),`cv::Vec4d`(四维双浮点(*double-precision float*)型矢量)。
总的来说`cv::Vec{2,3,4,6}{b,w,s,i,f,d}`的任意组合都是可行的，表示2到6维的6种数据类型。

除了fixed vector classes 外，还有fixed matrix classes，与模板类`cv::Matx<>`相关联。`cv::Matx<>`目的并非用于大数组操作，而是用于解决一些小矩阵问题，在计算机视觉中，有许多场景用到了2x2,3x3的矩阵，少量用到了4x4的矩阵。同样的`cv::Matx<>`也有别名，`cv::Matx{1,2,3,4,5,6}{1,2,3,4,5,6}{f,d}`.

与*fixed vector classes*紧密关联的是*point classes*，用来存储2到三个表示点位置的值。*point classes*与*fixed vector classes*最大的不同在于，它通过named variables来获取成员的值(mypoint.x,mypoint.y,ect),而*fixed vector classes*通过下标获取(myvec[0]),myvec[1],ect).它的别名有：cv::Point2i,cv::Point2f ...

类`cv::Scalar`本质上是一个四维的 point 。但是与point不同的是，Scalar获取数据通过下标，与vector相同，因为Scalar是由Vec<double,4>继承而来的。

`cv::Size和cv::Rect`，与*point classes*一样，它们由自己的模板类继承而来。 `cv::Size` is mainly distinguished by having data members width and height rather than x and y, while `cv::Rect` has all four. The class `cv::Size` is actually an alias for `cv::Size2i`.


### 2.4 Basic Types:Getting Down to Details <h id="2.4">
本节开始学习各个类型的用法，以及其提供的函数接口。源码在*opencv2/core/core.hpp*
1. The point classes
2. The cv::Scalar class
3. The Size classes
4. The cv::Rect class
5. The cv::RotatedRect class
6. The fixed matrix classes
7. The fixed vector classes
8. The complex number classes

#### 2.4.1 The point classes <h id="2.4.1">
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

#### 2.4.2 The cv::Salar class <h id="2.4.2">
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
#### 2.4.3 The Size classes <h id="2.4.3">
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

#### 2.4.4 The cv::Rect class <h id="2.4.4">
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


#### 2.4.5 The cv::RotatedRect class <h id="2.4.5">
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

#### 2.4.6 The fixed matrix classes <h id="2.4.6">
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
|Extract row *i*|m14f=m44f.row(i);|
|Extract column *j*|m41f=m44.col(j);|
|Extract matrix diagonal(对角线)|m41f=m44f.diag();|
|Compute transpose(转置？？？)|n44f=m44f.t();|
|Invert matrix(倒置)|n44f=m44f.inv(method);//默认方式是cv::DECOMP_LU|
|Solve linear system|m31f=m33f.solve(rhs31f,method);m32f=m33f.solve<2>(rhs32f,method);|
|Per-element multiplocation|m1.mul(m2);|

#### 2.4.7 The fixed vector classes <h id="2.4.7">
>Table 2.4.7 Function

|Operation|Example|
|---|---|
|Default constructor|Vec2s v2s;Vec6f v6f;|
|Copy constructor|Vec3f u3f(v3f);|
|Value constructors|Vec2f v2f(x0,x1);Vec6d v6d(x0,x1,x2,x3,x4,x5);|
|Member access|v4f[i];v3w(j);//使用()和[]都可以|
|Vector cross-product(向量叉乘？？)|v3f.cross(u3f);|

#### 2.4.8 The complex number classes <h id="2.4.8">
复数类

>Table 2.4.8 Function

|Operation|Example|
|---|---|
|Default constructor|cv::Complexf z1;cv::Complexf z2;|
|Copy constructor|cv::Complexf z2(z1);|
|Value constructors|cv::Complexd z1(re0);cv::Complexd(re0,im1);|
|Member access|z1.re;z1.im;|
|Complex conjugate(共轭)|z2=z1.conj();|

与其它的别名一样，`cv::Complexf`和`cv::Complexd`分别表示single-和double-精度的复数。

*2020/7/20 14:24*

### 2.5 Helper Object <h id="2.5">

除了基础数据类型与大容器(后面的章节会学习)，还有一些对于操作大量算法问题很重要的helper Object。例如通过智能指针对象`cv::Ptr`的学习，我们将认识到c++的垃圾回收系统，这是c++对于OpenCV必不可少的接口，这个系统帮助我们摆脱`allocation`和`deallocation`的烦恼。

#### 2.5.1 The cv::TermCriteria class <h id="2.5.1">

Many algorithms require a stopping condition to know when to quit.Generally, stopping criteria take the form of either some finite number of iterations that are allowed (called COUNT or MAX_ITER) or some kind of error parameter that basically says, “if you are this close, you can quit” (called EPS—short for epsilon, everyone’s favorite tiny number). In many cases, it is desirable to have both of these at once so that if the algorithm never gets “close enough,” it will still quit at some point.The cv::TermCriteria objects encapsulate one or both of the stopping criteria so that they can be passed conveniently to an OpenCV algorithm function. They have
three member variables—type, maxCount, and epsilon—which can be set directly (they are public) or, more often, are just set by the constructor with the form TermCri
teria( int type, int maxCount, double epsilon ). The variable type is set to
either cv::TermCriteria::COUNT or TermCriteria::EPS. You can also “or” (i.e., |)
the two together. The value cv::TermCriteria::COUNT is a synonym for cv::Term

*有待理解*

#### 2.5.2 The cv::Range class <h id="2.5.2">

`cv::Range`class被使用来声明一块连续的整数区域，它有2个元素，`start` and `end`.Ranges包括了它们的起始值，但是不包括结束值，即`cv::Range rng(0,4)`中包括了0，1，2，3但是不会包括4.

使用`size()`方法可以得知range里面有多少个元素，就上面而言，`rng.size()`会得到4.另外它还有`empty()`成员函数，用来检测是否有元素在range里面。
" Finally, cv::Range::all() can be used anywhere a range
is required to indicate whatever range the object has available."

#### 2.5.3 The cv::Ptr template and Garbage Collection 101 <h id="2.5.3">

会自动释放空间的智能指针

使用步骤，首先需要为你想“包装”的类对象定义一个指针模板类的实例：
~~~cpp
cv::Ptr<Matx33f> p(new cv::Matx33f);
//或者
cv::Ptr<Matx33f> p=makePtr<cv::Matx33f>();
~~~
可以如同使用正常指针一般使用*p*
*2020/7/21 10:58*
#### 2.5.4 The cv::Exception class and exception handling <h id="2.5.4">
例外情况的中断处理，OpenCV有自己的exceptions去处理错误，被定义为`cv::Exception`。事实上，这个exception type是从STL的std::exception中派生得到的，并且除了命名空间的不同，也没有其它很大的区别。

类型`cv::Exception`有成员`code`,`err`,`func`,`file`和`line`，分别代表错误代码编号，对错误的简要说明，出错函数名，出错的文件，出错的具体行数。

并且这里也有一些内置宏可以让你自己生成exception。`CV_Error(errorcode,description)` 会生成和抛出异常通过一些提前固定的文本描述，而`CV_Error_(errorcode,printf_fmt_str,[printf-args])`可以自定义抛出的描述的内容。`CV_Assert(condition)`就跟`Assert()`的用法一样！

#### 2.5.5 The cv::DataType<>template  <h id="2.5.5">

真看不懂 

#### 2.5.6 The cv::InputArray and cv::OutputArray classes <h id="2.5.6">

真看不懂 

### 2.6 Utility Functions效用函数 <h id="2.6">
本章之前的内容提供一些原始的数据类型，但除了这些OpenCV还提供了另外一些实用的便于解决计算机视觉上的数学算法问题的函数，这些函数AKA 效用函数(utility function)。 这些函数包括了数学操作，测试，错误生成，内存和线程优化等。加下来将讲解这些函数。
#### 2.6.1 cv::alignPtr() <h id="2.6.1">
将指针对齐到指定的字节数？？？

#### 2.6.2 cv::alignSize() <h id="2.6.2">
???

#### 2.6.3 cv::allocate()  <h id="2.6.3">
~~~cpp
template<T> T* cv::allocate(
	size_t sz
	);
~~~
与`new`风格的申请数组空间一样，它会allocate一个有sz个T类型成员的c风格的数组，并返回数组第一个对象的指针。

#### 2.6.4 cv::deallocate() <h id="2.6.4">
~~~cpp
template<T> void cv::deallocate(
	T* ptr, // Pointer to buffer to free
	size_t sz // size of buffer, multiples of sizeof(T)
);
~~~
与`delete`类似，它会释放一个有sz个T类型成员的c风格的数组。`cv::deallocate()`被用来释放那些`cv::allocate()`申请的空间(成对存在)。

#### 2.6.5 cv::fastAtan2() <h id="2.6.5">
~~~cpp
float cv::fastAtan2( // Return value is 32-bit float
	float y, // y input value (32-bit float)
	float x // x input value (32-bit float)
)
~~~
返回(x,y)的反正切值

#### 2.6.6 cvCeil() <h id="2.6.6">
~~~cpp
int cvCeil( // Return the smallest int >= x
float x // input value (32-bit float)
);
~~~
返回一个>=x的最小整数

#### 2.6.7 cvFloor() <h id="2.6.7">
~~~cpp
int cvFloor( // Return the largest int <= x
float x // input value (32-bit float)
);
~~~

返回一个<=x的整数

#### 2.6.8 cv::cubeRoot() <h id="2.6.8">
~~~cpp
float cv::cubeRoot( // Return value is 32-bit float
float x // input value (32-bit float)
)
~~~
计算并返回x的立方根

#### 2.6.9 cv::CV_Assert() <h id="2.6.9">
与`Assert()`用法一样

#### 2.6.10 CV_Error() and CV_Error_() <h id="2.6.10">
~~~cpp
// example
CV_Error( ecode, estring )
CV_Error_( ecode, fmt, ... )
~~~
The macro CV_Error() allows you to pass in an error code ecode and a fixed C-style
character string estring, which it then packages up into a cv::Exception that it then
passes to cv::error() to be handled. The variant macro CV_Error_() is used if you
need to construct the message string on the fly. CV_Error_() accepts the same ecode
as CV_Error(), but then expects a sprintf()-style format string followed by a vari‐
able number of arguments, as would be expected by sprintf()

#### 2.6.11 cv::error() <h id="2.6.11">
???
#### 2.6.12 cv::fastFree() and cv::fastMalloc() <h id="2.6.12">
~~~cpp 
void* cv::fastMalloc( // Pointer to allocated buffer
size_t size // Size of buffer to allocate
);

void cv::fastFree(
void* ptr // Pointer to buffer to be freed
);
~~~
cv :: FastMalloc（）的工作方式与您熟悉的malloc（）相同，除了更快，并且可以为您实现缓冲区大小对齐。 这意味着如果缓冲区传递的大小超过16个字节，返回的缓冲区将与16个字节对齐边界.

cv::fastFree()用来释放cv::fastFree申请的空间


#### 2.6.13 cv::format() <h id="2.6.13">
~~~cpp
string cv::format( // Return STL-string
const char* fmt, // formatting string, as sprintf()
... // vargs, as sprintf()
);
~~~
此函数与标准库中的sprintf（）基本相同，但是并不是要求调用者提供字符缓冲区，而是构造一个STL字符串对象并返回该对象。 这对于格式化错误消息特别方便。Exception（）构造函数（期望在其参数中包含STL字符串）

#### 2.6.14 cv::getCPUTickCount() <h id="2.6.14">
~~~cpp
int64 cv::getCPUTickCount( void ); // long int CPU for tick count
~~~
此功能报告具有以下特性的那些架构上的CPU滴答声数量
构造（包括但不限于x86架构）。 重要的是要知道，
但是，该函数的返回值可能很难解释
许多架构。 特别是因为在多核系统上可以放置线程
在一个核心上睡觉，在另一个核心上醒来，结果之间的差异
对cv :: getCPUTickCount（）的两个后续调用可能会误导或完全误导
无意义的。 因此，除非您确定自己知道自己在做什么，否则最好使用cv :: getTickCount（）进行时序测量。13此功能最适合
初始化随机数生成器之类的任务。
#### 2.6.15 cv::getNumThreads() <h id="2.6.15">
~~~cpp
int cv::getNumThreads( void ); // total threads allocated to OpenCV
~~~
返回OpenCV使用的线程数量


#### 2.6.16 cv::getOptimalDFTSize() <h id="2.6.16">
？？？
#### 2.6.17 cv::getThreadNum() <h id="2.6.17">
~~~cpp
int cv::getThreadNum( void ); // int, id of this particular thread
~~~
If your OpenCV library was compiled with OpenMP support, it will return the index (starting from zero) of the currently executing thread.

#### 2.6.18 cv::getTickCount() <h id="2.6.18">
~~~cpp
int64 cv::getTickCount( void ); // long int CPU for tick count
~~~

#### 2.6.19 cv::getTickFrequency() <h id="2.6.19">
~~~cpp
double cv::getTickFrequency( void ); // Tick frequency in seconds as 64-bit
~~~

#### 2.6.20 cvIsInf() <h id="2.6.20">
~~~cpp
int cvIsInf( double x ); // return 1 if x is IEEE754 "infinity"
~~~
返回1如果x是IEEE754 standard中的正负无穷大，否则返回0

#### 2.6.21 cvIsNaN() <h id="2.6.21">
~~~cpp
int cvIsNan( double x ); // return 1 if x is IEEE754 "Not a number"
~~~
返回1如果x不是数字，否则返回0.

#### 2.6.22 cvRound() <h id="2.6.22">
~~~cpp
int cvRound( double x ); // Return integer nearest to 'x'
~~~
返回一个离x最近的整数

#### 2.6.23 cv::setNumThreads() <h id="2.6.23">
~~~cpp
void cv::setNumThreads( int nthreads ); // Set number of threads OpenCV can use
~~~

#### 2.6.24 cv::setUseOptimized() <h id="2.6.24">
~~~cpp
void cv::setUseOptimized( bool on_off ); // If false, turn off optimized routines
~~~
???
#### 2.6.25 cv::useOptimized() <h id="2.6.25">
???

## 3. Images and Large Arrey Types 
### Dynamic and Variable Storage 动态可变存储
接下来将学习 *large array types* ,其中最主要的就是`cv::Mat`，OpenCV中大部分的函数都是`cv::Mat`的成员函数。`cv::Mat` 类被用来代表任意维度的密集数组(*dense array*)。相反，`cv::SparseMat`代表了稀疏数组。

>导航

3.1 <a href="#3.1">The cv::Mat Class:N-Dimensional Dense Arrays</a><br>
3.2 <a href="#3.2">Creating an Array</a><br>
3.3 <a href="#3.3">Accessing Array Element Individually</a><br>
3.4 <a href="#3.4">The N-ary Array Iterator:NAryMatlterator</a><br>

#### 3.1 The cv::Mat Class:N-Dimensional Dense Arrays <h id="3.1">
??? 
#### 3.2 Creating an Array <h id="3.2">
可以通过极其简单的声明变量的方法来创建一个Mat数组：
~~~cpp
cv::Mat m;
~~~
这时候得到`m`是没有尺寸大小和数据类型的，可以通过Mat的一个成员函数`create()`来为其申请内存空间大小以及存放的数据类型。

~~~cpp
cv::Mat m;
m.create(3,10,CV_32FC3);
m.setTo(cv::Scalar(1.0f,0.0f,1.0f));
~~~
上面的代码将`m`变成了一个*3rows 10 columns*的*三通道 32位float型*的数组。并且通过`setTo()`将其第一通道设置为1.0，第二通道设置为0.0，第三设置为1.0 。

>关于creat()
第一个参数表示行，第二个参数表示列，(当然这表示一个二维的平面，以后也可以增加参数来表示一个三维的立体)。第三个参数表示这个数组容纳什么类型的数据，包括数据的数据类型，通道数，这些都通过头文件的宏定义帮大家订好了。可以用以下的形式`CV_{8U,16S,16U,32S,32F,64F}C{1,2,3}`前面的表示是几位的什么类型的数据，比如32F表示32位的float，后面的表示通道数，比如3表示三通道。

另外，也可以将setTo写到create里面来一次完成，上面的代码等价为：
~~~cpp
cv::Mat m=(3,10,CV_32FC3,cv::Scalar(1.0f,0.0f,1.0f));
~~~

其实`cv::Mat`有多种声明方式，虽然我们大多数时候只会用到其中几种。如下表：
>Table 3.2.1

|Constructor|Description|
|---|---|
|cv::Mat;|Default constructor|
|cv::Mat( int rows, int cols, int type );|Two-dimensional arrays by type|
|cv::Mat(int rows, int cols, int type,const Scalar& s);|Two-dimensional arrays by type with initialization value|
|cv::Mat(int rows, int cols, int type,void* data, size_t step=AUTO_STEP);|Two-dimensional arrays by type with preexisting data|
|cv::Mat( cv::Size sz, int type );|Two-dimensional arrays by type (size in sz)|
|cv::Mat(cv::Size sz,int type, const Scalar& s);|Two-dimensional arrays by type with initialization value (size in sz)|
|cv::Mat(cv::Size sz, int type,void* data, size_t step=AUTO_STEP);|Two-dimensional arrays by type with preexisting data (size in sz)|
|cv::Mat(int ndims, const int* sizes,int type);|Multidimensional arrays by type|
|cv::Mat(int ndims, const int* sizes,int type, const Scalar& s);|Multidimensional arrays by type with initialization value|
|cv::Mat(int ndims, const int* sizes,int type, void* data,size_t step=AUTO_STEP);|Multidimensional arrays by type with preexisting data|

此外，这里还有一些`Copy`函数，可以复制一个`Mat` 到 另一个`Mat`

*2020/7/23 16:22*

>Table 3.2.2

|Constructor|Description|
|---|---|
|cv::Mat( const Mat& mat );|Copy constructor|
|cv::Mat(const Mat& mat,const cv::Range& rows,const cv::Range&cols)|Copy constructor that copies only a subset of rows and columns|
|cv::Mat(const Mat& mat,const cv::Rect& roi);|Copy constructor that copies only a subset of rows and columns specified by a region of interes|
|cv::Mat(const Mat& mat,const cv::Range* ranges);|Generalized region of interest copy constructor that uses an array of ranges to select from an n-dimensional array|
|cv::Mat( const cv::MatExpr& expr );|Copy constructor that initializes m with the result of an algebraic expression of other matrices|

还有三个创建特殊矩阵的函数 
>Table 3.2.3

|Function|Description|
|---|---|
|cv::Mat::zeros( rows, cols, type );|0矩阵|
|cv::Mat::ones( rows, cols, type );|全是1矩阵|
|cv::Mat::eye( rows, cols, type );|单位矩阵|

#### 3.3 Accessing Array Element Individually <h id="3.3">

我们有许多方法去访问一个矩阵内的元素，而在最近的OpenCV版本中，最常用的2种方法是寻址或者迭代。

最基本的寻址方式是使用模板类成员函数`at<>()`。你需要提供要访问的数据的类型以及它的矩阵内坐标，下面是一个简单的例子：
~~~cpp
cv::Mat m=cv::Mat::eye(10,10,CV_32FC1);

printf("Element(3,3) is %f/n",m.at<float>(3,3));

~~~

如果是一个多通道的大数组，则需要用下面的方法：
~~~cpp
cv::Mat m=cv::Mat::eye(10,10,CV_32FC2);
printf("Element(3,3) is (%f,%f)/n",m.at<cv::Vec2f>(3,3)[0],m.at<cv::Vec2f>(3,3)[1]);
~~~
记住，当需要去访问一个多通道数组时，最好使用cv::Vec<>对象。

*2020/7/24 11:06*

#### 3.4 The N-ary Array Iterator:NAryMatlterator <h id="3.4">


