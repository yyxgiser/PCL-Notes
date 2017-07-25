# 点云可视化

可视化（Visualization）是利用计算机图形学和图像处理技术，将数据转换成图形或图像在屏幕显示出来，并且进行交互处理的理论、方法和技术。

PCL中pcl\_visualization库中提供了可视化相关的数据结构和组件，其主要是为了可视化其他模块算法处理后的结果，可直观的反馈给用户。其依赖于pcl\_common、pcl\_range\_image、pcl\_kdtree、pcl\_IO模块以及VTK外部开源可视化库。下面给出2个常用的可视化类。

* **pcl::visualization::PCLVisualizer**

PCLVisualizer是PCL可视化3D点云的主要类。其内部实现了添加各种3D对象以及交互的实现等，比其他类实现的功能更齐全。

**基础显示功能：** 显示点云、网格、设置颜色、连线

```
#include <pcl/io/pcd_io.h>
#include <pcl/io/ply_io.h>
#include <pcl/point_types.h>
// 包含相关头文件
#include <pcl/visualization/pcl_visualizer.h>

typedef pcl::PointXYZ PointT;

int main()
{
	// 读取点云
	pcl::PointCloud<PointT>::Ptr cloud1(new pcl::PointCloud<PointT>);
	pcl::io::loadPCDFile("read1.pcd", *cloud1);

	pcl::PointCloud<PointT>::Ptr cloud2(new pcl::PointCloud<PointT>);
	pcl::io::loadPCDFile("read2.pcd", *cloud2);

	// 定义对象
	pcl::visualization::PCLVisualizer viewer; 
	//设置背景颜色，默认黑色
	viewer.setBackgroundColor(100, 100, 100); // rgb

	// --- 显示点云数据 ----
	// "cloud1" 为显示id，默认cloud,显示多个点云时用默认会报警告。
	viewer.addPointCloud(cloud1, "cloud1"); 

	pcl::visualization::PointCloudColorHandlerCustom<PointT> red(cloud2, 255, 0, 0); // rgb
	// 将点云设置颜色，默认白色
	viewer.addPointCloud(cloud2, red, "cloud2");

	// 将两个点连线
	PointT temp1 = cloud1->points[0];
	PointT temp2 = cloud1->points[1];
	viewer.addLine(temp1, temp2, "line0"); 
	// 同样可以设置线的颜色，
	//viewer.addLine(temp1, temp2, 255，0，0， "line0");

	// --- 显示网格数据 ---
	pcl::PolygonMesh mesh;
	pcl::io::loadPLYFile("read.ply", mesh);

	viewer.addPolygonMesh(mesh);


	// 开始显示2种方法,任选其一
	// 1. 阻塞式
	viewer.spin();

	// 2. 非阻塞式
	while (!viewer.wasStopped())
	{
		viewer.spinOnce(100);
		boost::this_thread::sleep(boost::posix_time::microseconds(100000));
		// 可添加其他操作
	}

	system("pause");
	return 0;
}
```

**高级功能：**设置回掉函数进行交互、设置显示区域

	+ 按键事件

