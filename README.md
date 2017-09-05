## Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  

You're reading it!



## Exercise 1, 2 and 3 pipeline implemented

#### 1. Complete Exercise 1 steps. Pipeline for filtering and RANSAC plane fitting implemented.

First task was to get acquainted with RGB-D and PointCloud. In this case we already had a PointCloud file (a table with some objects on it). The first task was to filter the unnecessary data (pointcloud noise) and work with just points of interest: 

Filters used for getting those points:
​	- VoxelGrid Downsampling Filter
​	- ExtractIndices Filter
​	- Pass-through Filter
​	- RANSAC Plane Fitting
​	- Outliers Removal Filter

And at the end, we get the object separated from other part of the image (this case table: pictures below)

![demo-1](https://raw.githubusercontent.com/bresilla/robond_project_3/master/assets/Screenshot-20170817212030-3840x2160.png)



![demo-1](https://raw.githubusercontent.com/bresilla/robond_project_3/master/assets/Screenshot-20170817212230-3840x2160.png)



#### 2. Complete Exercise 2 steps: Pipeline including clustering for segmentation implemented.

In second step, we had to cluster the points based on DB-SCAN algorithm (Euclidean algorithm). DBSCAN is a more fancy algorithm than K-Means as it works even if you don't know how many clusters you are going to have (the implementation is in segmentation.py of sensor-stick package).

![demo-1](https://raw.githubusercontent.com/bresilla/robond_project_3/master/assets/Screenshot-20170817213408-3840x2160.png)

After that, we published the clusters to ros core:

```python
pcl_cluster_pub = rospy.Publisher("/pcl_cluster", PointCloud2, queue_size = 1)
```

![demo-1](https://raw.githubusercontent.com/bresilla/robond_project_3/master/assets/Screenshot-20170817212030-3840x2160.png)



#### 3. Complete Exercise 3 Steps.  Features extracted and SVM trained.  Object recognition implemented.

We used SMV to trian a model capture features and learn to recognize objects.

As feature to be extracted/detected/learned was used the normal histograms.

We used Gazebo as environment where the object/models below in the list will be the features taken from. For each model different number of poses were taken.

```python
    models = [\
       'biscuits',
       'soap',
       'soap2',
       'book',
       'glue',
       'sticky_notes',
       'snacks',
       'eraser']
```

With 50 poses we achieved ~60% accuracy:![demo-1](https://raw.githubusercontent.com/bresilla/robond_project_3/master/assets/Screenshot-20170818210855-3840x2160.png)

With 100 poses we achieved ~80% accuracy:

![demo-1](https://raw.githubusercontent.com/bresilla/robond_project_3/master/assets/Screenshot-20170819133330-3840x2160.png)

With 100 poses and HSV color conversion we achieved ~90% accuracy:

![demo-1](https://raw.githubusercontent.com/bresilla/robond_project_3/master/assets/Screenshot-20170819141026-3840x2160.png)

Here the labels published to ROS after more than 90% accuracy reached:

![demo-1](https://raw.githubusercontent.com/bresilla/robond_project_3/master/assets/Screenshot-20170820103404-3840x2160.png)





## Pick and Place Setup

Well, basically the hard work is done, now we just hd to put all pieces together:

The picture below shows the first world after 100 poses training but without normal histograms, the confusion matrix was very confused :) :) :)

![demo-1](https://raw.githubusercontent.com/bresilla/robond_project_3/master/assets/Screenshot-20170902140601-3840x2160.png)

And in the simulator, the labels were completely wrong (sometimes right, but just because of chance):

![demo-1](https://raw.githubusercontent.com/bresilla/robond_project_3/master/assets/Screenshot-20170902140717-3840x2160.png)

In world 3, those models were captured around 500 poses:

```python
    models = [\
       'biscuits',
       'soap',
       'soap2',
       'book',
       'glue',
       'sticky_notes',
       'snacks',
       'eraser']
```

And with HSV conversion, the accuracy was 82%.

![demo-1](https://raw.githubusercontent.com/bresilla/robond_project_3/master/assets/Screenshot-20170903181545-3840x2160.png)

Objects correctly labeled:

![demo-1](https://raw.githubusercontent.com/bresilla/robond_project_3/master/assets/Screenshot-20170903184315-3840x2160.png)

To increase accuracy, apart from HSV conversion, increased number of training data (poses) i increased number of bin size from 32 to 64 and the reached more than 95% of accuracy.

![demo-1](https://raw.githubusercontent.com/bresilla/robond_project_3/master/assets/Screenshot-20170903213229-3840x2160.png)
