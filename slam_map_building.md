# Demo：slam建图

## 前期了解：
* 本次实验需要涉及到哪些文件这些文件的作用是什么？关键参数说明（如果重要），目录结构说明
* Parameter：
	1. param_1:速度参数
	2. param_2:方向参数
	3. ...
* File： 
	1. start_transfer.sh: 传递机器狗的运动参数
		- 关键函数：move(parameter_1,parameter_2,...)
	2. start_lslidar.sh:  开启机器狗的雷达
	3. start_slam.sh:  开启slam建图功能 
		- 
* Directory structure：(标注Demo所需关键文件)
```
lite_cog/                     # 项目根目录
│
├── docs/                         # 文档目录
│   ├── requirements.md           # 需求文档
│   ├── design.md                 # 设计文档
│   ├── api/                      # API文档
│   └── diagrams/                 # 系统架构图/流程图
│
├── src/                          # 源代码目录
│   ├── main/                     # 主代码
│   │   ├── java/                 # Java代码 (或其他语言)
│   │   ├── resources/            # 资源文件
│   │   └── webapp/               # Web应用资源 (如适用)
│   │
│   └── test/                     # 测试代码
│       ├── java/                 # 测试代码
│       └── resources/            # 测试资源
│
├── lib/                          # 第三方库/依赖
├── build/                        # 构建输出目录
│   ├── classes/                  # 编译后的类文件
│   ├── reports/                  # 测试报告
│   └── dist/                     # 发布包
│
├── config/                       # 配置文件目录
│   ├── dev/                      # 开发环境配置
│   ├── test/                     # 测试环境配置
│   └── prod/                     # 生产环境配置
│
├── scripts/                      # 脚本目录
│   ├── deploy.sh                 # 部署脚本
│   ├── build.sh                  # 构建脚本
│   └── db/                       # 数据库脚本
│
├── logs/                         # 日志文件目录
├── tmp/                          # 临时文件目录
│
├── README.md                     # 项目说明文件
├── LICENSE                       # 许可证文件
└── .gitignore                    # Git忽略规则

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
* 步骤2：检测主从机是否配置成功
	* 主机中打开终端，输入命令：roscore
	  新建另一个终端，输入命令：rosrun turtlesim turtle_teleop_key
	* 从机上打开终端输入命令：rosrun turtlesim turtlesim_node
	* 输入后从机会弹出一个有小乌龟窗口，若在主机上使用键盘能控制从机小乌龟的运动，即主从机配置成功
* 步骤3：执行杀死进程脚本
	* 如果之前有进行过建图操作，需要先执行以下命令（若没有直接跳到下一步），避免影响后续进程
 	* 新建终端，输入以下命令：
		cd /home/ysc/lite_cog/system
		./kill_all.sh
* 步骤4：打开transfer
	* 从机中打开终端，输入以下命令（后续操作都在从机上进行）：   
		cd /home/ysc/lite_cog/system/scripts/tranfer
		./start_transfer.sh
* 步骤5：打开感应雷达
	* 新建终端，输入以下命令：
		cd /home/ysc/lite_cog/system/scripts/lidar
		./start_lslidar.sh
* 步骤6：开启点云建图程序
	* 新建终端，输入以下命令：
		cd /home/ysc/lite_cog/system/scripts/slam
		./start_slam.sh
	* 回车执行命令后，会弹出两个终端和rviz界面。两个终端分别对应生成栅格地图和保存栅格地图
* 步骤7：遥控机器狗建图
	* 遥控机器狗环绕需要构建地图的区域行走
	* 行走完后进入创建图像对应终端按“1”，将三维点云文件（.pcd）转换为栅格地图（.pcm），当弹出rviz窗口有对应栅格地图后可执行下一步
	* 进入保存图像对应终端按“2”保存图像，默认保存路径为/home/ysc/lite_cog/system/map，会有lite3.pgm,lite3.pgd,lite3.yaml等文件
              #若文件夹中有以前建立的地图，可将以前的建图移动到其它文件夹，避免覆盖
	* 若栅格地图与实际环境有不相符之处，可打开终端输入gimp打开修图软件，将栅格地图文件拖到软件中进行修改。其中黑色为障碍物，白色为可通行区域，灰色为未知区域
        * 修改后在左上方菜单栏选择File - Overwrite usr_map.pgm对原文件进行覆盖（注意不要选择File - Save）
        * 
	
.

			

.


## 问题说明 & 解决

* 问题1：（说明简述即可不需要把error全部粘贴）
	* 解决说明：
		* ……
	* 解决步骤：
		1. 进行什么什么
		2. 运行什么什么
		3. 安装什么什么
