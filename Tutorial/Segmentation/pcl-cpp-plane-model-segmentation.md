# [[PCL-Cpp] Plane model segmentation](http://www.pointclouds.org/documentation/tutorials/planar_segmentation.php)

> [How to use Random Sample Consensus model](http://pointclouds.org/documentation/tutorials/random_sample_consensus.php#random-sample-consensus) 선행 학습 필요 
> [Extracting indices from a PointCloud](http://pointclouds.org/documentation/tutorials/extract_indices.php#extract-indices)의 선수과목 

In this tutorial we will learn how do a simple plane segmentation of a set of points, that is find all the points within a point cloud that support a plane model. 

This tutorial supports the Extracting indices from a PointCloud tutorial, presented in the filtering section.

```cpp

#include <iostream>
#include <pcl/ModelCoefficients.h>
#include <pcl/io/pcd_io.h>
#include <pcl/point_types.h>
#include <pcl/sample_consensus/method_types.h>
#include <pcl/sample_consensus/model_types.h>
#include <pcl/segmentation/sac_segmentation.h>

int
 main (int argc, char** argv)
{
  pcl::PointCloud<pcl::PointXYZ>::Ptr cloud(new pcl::PointCloud<pcl::PointXYZ>);

  // Fill in the cloud data
  cloud->width  = 15;
  cloud->height = 1;
  cloud->points.resize (cloud->width * cloud->height);

  // Generate the data
  for (size_t i = 0; i < cloud->points.size (); ++i)
  {
    cloud->points[i].x = 1024 * rand () / (RAND_MAX + 1.0f);
    cloud->points[i].y = 1024 * rand () / (RAND_MAX + 1.0f);
    cloud->points[i].z = 1.0;
  }

  // Set a few outliers
  //we manually added a few outliers in the data, by setting their z values different from 0.
  cloud->points[0].z = 2.0;
  cloud->points[3].z = -2.0;
  cloud->points[6].z = 4.0;

  pcl::ModelCoefficients::Ptr coefficients (new pcl::ModelCoefficients);
  pcl::PointIndices::Ptr inliers (new pcl::PointIndices);
  
  // Create the segmentation object
  pcl::SACSegmentation<pcl::PointXYZ> seg; 
  seg.setOptimizeCoefficients (true);  // Optional
  // Mandatory
  seg.setModelType (pcl::SACMODEL_PLANE); //PLANE 모델 사용
  seg.setMethodType (pcl::SAC_RANSAC);  //RANSAC 방법 사용 
  seg.setDistanceThreshold (0.01); //determines how close a point must be to the model in order to be considered an inlier
  
  seg.setInputCloud (cloud);
  seg.segment (*inliers, *coefficients);


  //모델 파라미터 출력 estimated plane parameters (in ax + by + cz + d = 0
  std::cerr << "Model coefficients: " << coefficients->values[0] << " " 
                                      << coefficients->values[1] << " "
                                      << coefficients->values[2] << " " 
                                      << coefficients->values[3] << std::endl;



  return (0);
}


```

```




---
# [Publishing and Subscribing to different point cloud message types](http://wiki.ros.org/pcl/Overview)



## with ROS 

```cpp
#include <ros/ros.h>
#include <sensor_msgs/PointCloud2.h>
#include <pcl_conversions/pcl_conversions.h>
#include <pcl/point_cloud.h>
#include <pcl/point_types.h>

#include <pcl/sample_consensus/model_types.h>
#include <pcl/sample_consensus/method_types.h>
#include <pcl/segmentation/sac_segmentation.h>
## http://wiki.ros.org/pcl/Tutorials



ros::Publisher pub;

void 
cloud_cb (const sensor_msgs::PointCloud2ConstPtr& input)
{
  // Convert the sensor_msgs/PointCloud2 data to pcl/PointCloud
  pcl::PointCloud<pcl::PointXYZ> cloud;
  pcl::fromROSMsg (*input, cloud);

  pcl::ModelCoefficients coefficients;
  pcl::PointIndices inliers;
  // Create the segmentation object
  pcl::SACSegmentation<pcl::PointXYZ> seg;
  // Optional
  seg.setOptimizeCoefficients (true);
  // Mandatory
  seg.setModelType (pcl::SACMODEL_PLANE);
  seg.setMethodType (pcl::SAC_RANSAC);
  seg.setDistanceThreshold (0.01);

  seg.setInputCloud (cloud.makeShared ());
  seg.segment (inliers, coefficients);

  // Publish the model coefficients
  pcl_msgs::ModelCoefficients ros_coefficients;
  pcl_conversions::fromPCL(coefficients, ros_coefficients);
  pub.publish (ros_coefficients);
}

int
main (int argc, char** argv)
{
  // Initialize ROS
  ros::init (argc, argv, "my_pcl_tutorial");
  ros::NodeHandle nh;

  // Create a ROS subscriber for the input point cloud
  ros::Subscriber sub = nh.subscribe ("input", 1, cloud_cb);

  // Create a ROS publisher for the output point cloud
  #pub = nh.advertise<sensor_msgs::PointCloud2> ("output", 1);
  pub = nh.advertise<pcl_msgs::ModelCoefficients> ("output", 1);

  // Spin
  ros::spin ();
}

```