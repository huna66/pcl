# Ubuntu18.04安装PCL 1.9.1
•	安装pcl所需依赖
sudo apt-get update  
sudo apt-get install git build-essential linux-libc-dev
sudo apt-get install cmake cmake-gui
sudo apt-get install libusb-1.0-0-dev libusb-dev libudev-dev
sudo apt-get install mpi-default-dev openmpi-bin openmpi-common
sudo apt-get install libflann1.9 libflann-dev
sudo apt-get install libeigen3-dev
sudo apt-get install libboost-all-dev
sudo apt-get install libvtk7.1-qt
sudo apt-get install libvtk7.1
sudo apt-get install libvtk7-qt-dev
sudo apt-get install libqhull* libgtest-dev
sudo apt-get install freeglut3-dev pkg-config
sudo apt-get install libxmu-dev libxi-dev
sudo apt-get install mono-complete
sudo apt-get install openjdk-8-jdk openjdk-8-jre

     我在安装这些依赖包发现我的unbuntu之前安装没有进行换源操作，导致我出现没有权限root问题，最后通过换源操作后才实现。
安装vtk
视觉化工具函数库（VTK，Visualization Toolkit）是一个开源、跨平台的系统，主要用于三维计算机图形学、图像处理和可视化等。

1. 先安装cmake 和vtk 的依赖项

sudo apt-get install cmake-curses-gui
sudo apt-get install freeglut3-dev

2.下载VTK-8.2.0库
https://vtk.org/download/#previous
进入vtk官网下载8.2.0的压缩包


点击Download the previous release (8.2.0)，然后选择下载下面任意一种类型的压缩包。因为zip和tar.gz都可以在ubuntu中手动解压。

3.解压VTK-8.2.0

进入压缩包的下载目录，然后右击【提取到此处】，当然也可以命令行解压，这里怎么简单怎么来。然后打开终端，进入VTK-8.2.0文件夹。
创建build文件夹，然后cd到build中， 使用可视化的cmake进行工程分析

mkdir build
cd  build
cmake-gui

4.配置VTK-8.2.0

选择文件路径，并勾选Grouped和Advanced， 如果看不到红色部分的内容，可以先点击底下的configure按钮，就可以出现红色部分的内容。展开Module和VTK，然后分别在里面配置勾选这两个：Module_vtkGUISupportQt、VTK_Group_Qt
如果一切正常，可以发现红色部分全都变为了白色，若发现有部分红色内容，则将文件夹build中内容删除后，重新点击configure就ok了。这里其实就相当于进行了cmake分析。

5.编译安装VTK-8.2.0

Ctrl+Z或者手动关闭cmake-gui界面。然后输入sudo make进行编译，过程会比较漫长。

最后输入sudo make install进行安装，大概5s不到，至此VTK-8.2.0安装成功。


编译安装VTK-8.2.0
Ctrl+Z或者手动关闭cmake-gui界面。然后输入sudo make进行编译，过程会比较漫长。
最后输入sudo make install进行安装，大概5s不到，至此VTK-8.2.0安装成功。
1、下载pcl 1.9.1
首先进入pcl的下载官网，没错，它官网没有源代码，都在github： https://github.com/PointCloudLibrary/pcl.git
然后点击右侧中间的Release，进入历史版本界面：
这里我开始安装的是zip，但是发现执行不了，最后安装的 tar.gz



编译安装pcl 1.9.1
解压下载的压缩包，然后打开终端，进入解压的目录，输入下面命令：
cd pcl-pcl-1.9.1
mkdir release
cd release
然后复制下面命令回车，用cmake分析整个源代码

cmake -DCMAKE_BUILD_TYPE=None -DCMAKE_INSTALL_PREFIX=/usr \ -DBUILD_GPU=ON-DBUILD_apps=ON -DBUILD_examples=ON \ -DCMAKE_INSTALL_PREFIX=/usr ..
出现了问题：

这个问题是因为前面的依赖包没有完全实现，返回前面解决问题。
这个问题解决是通过mkdir build && cd build
cmake -DCMAKE_BUILD_TYPE=None -DCMAKE_INSTALL_PREFIX=/usr \ -DBUILD_GPU=ON-DBUILD_apps=ON -DBUILD_examples=ON \ -DCMAKE_INSTALL_PREFIX=/usr ..
 make -j2
后输入sudo make install 进行安装





5、测试pcl
1、创建一个test_pcl文件夹，然后创建一个源文件test_pcl.cpp，代码如下
#include <iostream>
#include <pcl/common/common_headers.h>
#include <pcl/io/pcd_io.h>
#include <pcl/visualization/pcl_visualizer.h>
#include <pcl/visualization/cloud_viewer.h>
#include <pcl/console/parse.h>
using namespace std;
 
int main(int argc, char **argv) {//柱型点云测试
  cout << "Test PCL !" << endl;
  pcl::PointCloud<pcl::PointXYZRGB>::Ptr point_cloud_ptr (new pcl::PointCloud<pcl::PointXYZRGB>);
  uint8_t r(255), g(15), b(15);
  for (float z(-1.0); z <= 1.0; z += 0.05) {//制作柱型点云集
  	for (float angle(0.0); angle <= 360.0; angle += 5.0) {
      pcl::PointXYZRGB point;
      point.x = cos (pcl::deg2rad(angle));
      point.y = sin (pcl::deg2rad(angle));
      point.z = z;
      uint32_t rgb = (static_cast<uint32_t>(r) << 16 | static_cast<uint32_t>(g) << 8 | static_cast<uint32_t>(b));
      point.rgb = *reinterpret_cast<float*>(&rgb);
      point_cloud_ptr->points.push_back (point);
    }
    if (z < 0.0) {//颜色渐变
      r -= 12;
      g += 12;
    }
    else {
      g -= 12;
      b += 12;
    }
  }
  
  point_cloud_ptr->width = (int) point_cloud_ptr->points.size ();
  point_cloud_ptr->height = 1;
 
  pcl::visualization::CloudViewer viewer ("pcl—test测试");

  viewer.showCloud(point_cloud_ptr);
  while (!viewer.wasStopped()){ };
  return 0;
}
2、在test_pcl文件夹下再创建一个配置文件CMakeLists.txt，代码如下：
cmake_minimum_required(VERSION 2.6)
project(test_pcl)
 
find_package(PCL 1.2 REQUIRED)
 
include_directories(${PCL_INCLUDE_DIRS})
link_directories(${PCL_LIBRARY_DIRS})
add_definitions(${PCL_DEFINITIONS})
 
add_executable(test_pcl test_pcl.cpp)
 
target_link_libraries (test_pcl ${PCL_LIBRARIES})
 
install(TARGETS test_pcl RUNTIME DESTINATION bin)


都用touch实现文件夹

、编译源代码
在test_pcl文件夹下再建一个build文件夹，在终端中进入build文件夹下。
输入cmake ..进行工程分析，然后输入make进行编译。
4、运行源代码
编译成功后，输入./test_pcl运行可执行文件，运行成功！出现下面可以自由拉伸旋转的点云图！







参考文献：
https://blog.csdn.net/qq_42257666/article/details/124574029?ops_request_misc=&request_id=&biz_id=102&utm_term=unbuntu18.04%E5%AE%89%E8%A3%85pcl&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-2-124574029.142^v32^pc_rank_34,185^v2^control&spm=1018.2226.3001.4187
