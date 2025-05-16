# Demo：slam建图

## 前期了解：
* Parameter：
	
 
* File： 
	1. start_transfer.sh: 传递机器狗的运动参数
		- 
	2. start_lslidar.sh:  开启机器狗的雷达
	3. start_slam.sh:  开启slam建图功能 
		- 
* Directory structure：(标注Demo所需关键文件)
```
Multi_robot/
├── pipline/
|  ├──build/
|  ├──devel/
|  ├──src/					
│  	├── pipeline_tracking/			
|           ├──action				
|           ├──data				
|           ├──Readme				
|           ├──scripts/
|		├──__pycache__
|		├──.vscode
|               ├──constants.py
|               ├──dyn_reconfigure.py
|		├──MT.py
|		├──pub_target.py
|               ├──RobotCommander.py
|		├──start_MT.sh			#可以在Rviz中执行多目标跟踪、目标发布等任务
|		├──TA_minmax.py
|               ├──Task.py
|               ├──TaskPoint.py
|               └──TaskTransfer.py		
|           ├──tools				
|           ├──CMakeLists.txt		
|           └──package.xml			
|   └── CMakeLists.txt 				
└──.catkin_workspace				


lite_cog/                			#项目根目录
│
├── deb/                       			
├── drivers/					                  
├── nav/					#导航模块
|   ├──build/					#编译生成目录，存放编译中间文件
|   ├──devel/					#开发环境目录，存放编译后的可执行文件
|   └──src/					#导航功能源代码
│      ├──fast_gicp				#基于GICP算法，提供快速的点云配准算法实现，用于高精度地图匹配
│      ├──hdl_global_localization		#全局定位功能包，用于在已知地图中确定初始位置
│      ├──hdl_localization/			#定位功能包
|         ├──apps
|         ├──data
|         ├──docker
|         ├──include
|         ├──launch/				#启动文件目录
|            ├──hdl_localization.launch
|            └──local_rslidar_imu.launch    	#用于改变地图路径或名称时配置所需地图名称和路径
|         ├──msg
|         ├──rviz/				#Rviz配置文件
|            └──hdl_localization.rviz		#定位可视化配置
|         ├──scripts
|         ├──src
|         ├──CMakeLists.txt
|         ├──LICENSE
|         ├──nodelet_plugins.xml
|         ├──package.xml			#ROS功能包描述文件
|         └──README.md				#功能包使用说明
│      ├──move_base				#实现路径规划和导航控制
│      ├──navigation
│      ├──ndt_omp
│      ├──teb_local_planner
│      └──CMakeLists.txt                
├── others/                    		
├── pipeline/					
|   ├──bulid/					#编译生成目录
|   ├──devel/					#开发环境目录
|   └──src/					
│       ├── pipeline_tracking/			
|           ├──action				
|           ├──data				
|           ├──Readme				
|           ├──scripts
|           ├──tools				
|               ├──location_record.py		
|               └──location_record_en.py	
|           ├──CMakeLists.txt			
|           └──package.xml			
|      └── CMakeLists.txt 			
├── slam/
|   ├──build/					#编译生成目录
|   ├──devel/					#开发环境目录
|   └──src/					#建图功能源代码
|      ├──faster-lio				#构建3D点云地图(.pcd)
|      ├──map_server				#保存和加载栅格地图(.pgm)
|      ├──octomap_mapping			#构建3D占用地图
|      ├──pcd2grid				#将三维点云地图(.pcd)转为栅格地图(.pgm)并发布
|      └──CMakeLists.txt		        #SLAM模块编译配置    
├── system/					
|   ├──map/					#地图文件存储目录
|      ├──lite3.pcd				#3D点云地图文件
|      ├──lite3.pgm				#2D栅格地图图像文件
|      └──lite3.yaml				#地图配置文件，定义地图原点、分辨率等参数
|   ├──scripts/					#系统脚本目录
|      ├──depth_camera				#深度相机相关脚本
|      ├──lidar/				#激光雷达相关脚本
|         ├──start_livox.sh			#启动Livox系列激光雷达
|         └──start_lslidar.sh			#启动LS系列激光雷达
|      ├──nav/					#导航相关脚本
|         └──start_nav.sh			#启动导航功能
|      ├──slam/					#SLAM相关脚本
|         ├──gridmap.sh				#创建2D栅格地图
|         ├──save_map.sh			#保存地图
|         └──start_slam.sh			#启动建图程序
|      ├──transfer/				#数据传输相关脚本
|         └──start_transfer.sh			#启动数据传输功能
|      └──voa					#语音交互相关脚本
|   ├──all_start.sh				#实现开机自启动
|   └──kill_all.sh				#一键停止所有模块
├── track                         		
├── transfer                  				
└── voa                  			

```
## 步骤：
* 步骤1：主从机配置连接
	* 主机中打开终端，输入命令：gedit ~/.bashrc
		- 修改如下命令：
 
		. 		

			 export ROS_HOSTNAME=本机IP地址
			 export ROS_MASTER_URI=http://主机IP:11311  #主机中三个IP地址相同
			 export ROS_IP=主机IP
  
		.
	* 从机中打开终端，输入命令：gedit ~/.bashrc
		- 修改如下命令：
  
		.

			export ROS_HOSTNAME=本机IP地址           #从机在WiFi下对应的IP地址
			export ROS_MASTER_URI=http://主机IP:11311
			export ROS_IP=本机IP
		.

	* NoMachine中点击edit connection将Host改为从机在WiFi下对应的IP地址
 	* 主机和从机都是连在Xiaomi_2A1C，不能访问外网
* 步骤2：检测主从机是否配置成功
	* 主机中打开终端，输入命令：roscore
	  新建另一个终端，输入命令：rosrun turtlesim turtle_teleop_key
	* 从机上打开终端输入命令：rosrun turtlesim turtlesim_node
	* 输入后从机会弹出一个有小乌龟窗口，若在主机上使用键盘能控制从机小乌龟的运动，即主从机配置成功
* 步骤3：执行杀死进程脚本（这一步以及后续操作都在从机上进行）
	* 如果之前有进行过建图操作，需要先执行以下命令（若没有直接跳到下一步），避免影响后续进程
 	* 如果终端显示是处于conda环境中，输入命令：conda deactivate，避免影响后续操作（之后进行所有的步骤都需要先隔离conda环境）		 
 	* 新建终端，输入以下命令：
  
		.

			cd /home/ysc/lite_cog/system
			./kill_all.sh
		.
	
* 步骤4：打开transfer
	* 从机中打开终端，输入以下命令：

  		.
   
			cd /home/ysc/lite_cog/system/scripts/tranfer
			./start_transfer.sh
   		.
* 步骤5：打开感应雷达
	* 新建终端，输入以下命令：

   		.

			cd /home/ysc/lite_cog/system/scripts/lidar
			./start_lslidar.sh
   		.
* 步骤6：开启点云建图程序
	* 新建终端，输入以下命令：

  		.

			cd /home/ysc/lite_cog/system/scripts/slam
			./start_slam.sh
   		.
	* 回车执行命令后，会弹出两个终端和rviz界面。两个终端分别对应生成栅格地图和保存栅格地图
* 步骤7：遥控机器狗建图
	* 遥控机器狗环绕需要构建地图的区域行走
	* 行走完后进入创建图像对应终端按“1”，将三维点云文件（.pcd）转换为栅格地图（.pcm），当弹出rviz窗口有对应栅格地图后可执行下一步
	* 进入保存图像对应终端按“2”保存图像，默认保存路径为/home/ysc/lite_cog/system/map，会有lite3.pgm,lite3.pgd,lite3.yaml等文件
              #若文件夹中有以前建立的地图，可将以前的建图移动到其它文件夹，避免覆盖
	* 若栅格地图与实际环境有不相符之处，可打开终端输入gimp打开修图软件，将栅格地图文件拖到软件中进行修改。其中黑色为障碍物，白色为可通行区域，灰色为未知区域
 	* 修改后在左上方菜单栏选择File - Overwrite usr_map.pgm对原文件进行覆盖（注意不要选择File - Save）
	* 如果需要改变地图文件保存路径或文件名称，需在/home/ysc/lite_cog/nav/src/hdl_localization/launch/local_rslidar_imu.launch文件中配置所需地图名称和路径

    		.

   			1  <arg name="map_name" default="lite3" /> //Define Map File Name
  			2  ...
  			3  <node name="MapServer" pkg="map_server" type="map_server"
   			   args="/home/ysc/lite_cog/system/map/$(arg map_name).yaml"/>
   			4
  			5  ...
  	 		6  ...
  			7  <paramname="globalmap_pcd"value="/home/ysc/lite_cog/system/map/$(arg map_name).pcd"
    			8  /> 
			   ...
   		.	

## 问题说明 & 解决

* 问题1：RLException: unable to contact ROS master
	* 解决说明：
		* 这个报错说明主从机没有配置成功
	* 解决步骤：
		1. 执行步骤2
  		2. 若仍有报错，检查主从机IP地址是否正确，重启NoMachine
		3. 若还有报错，试着重启机器狗
  		4. 如果还是报错，可能是机器狗本身通信问题 
