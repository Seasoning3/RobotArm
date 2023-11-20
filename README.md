# RobotArm

https://zeta7.notion.site/zeta7/JessiArm-be431f54912b472fb7f8977e5499612d

zeta님의 글을 참고했습니다.

# Python3 설치

~~~shell
sudo apt install python3-pip
~~~

# ROS Melodic 설치
~~~shell
cd ~/Downloads/
sudo apt update

git clone https://github.com/zeta0707/installROS.git
cd installROS
./install-ros.sh
~~~

# catkin workspace 설치

~~~shell
jetson@jp4512G:~/catkin_ws$ cca
jetson@jp4512G:~/catkin_ws$ cma
~~~

# JessiArm 설치

~~~shell
$ cd ~/catkin_ws/src
$ git clone https://github.com/zeta0707/jessiarm.git
$ cd ~/catkin_ws
~~~

# Joystick 조작

패키지 설치

~~~shell
$ sudo apt-get install joystick
$ cd /dev/input
$ ls js*
~~~

launch 파일로 실행

~~~shell
$ roslaunch jessiarm_joy joy_teleop_axes_jetson.launch
~~~

# keyboard 조작

teleop_twist_keyboard 설치

~~~shell
jetson@jp4612GCv346Py37:~/catkin_ws$ git clone https://github.com/ros-teleop/teleop_twist_keyboard.git
jetson@jp4612GCv346Py37:~/catkin_ws$ cd ..
jetson@jp4612GCv346Py37:~/catkin_ws$ cma
~~~

launch 파일로 실행

~~~shell
$ roslaunch jessiarm_control teleop_keyboard.launch
~~~

# 자동 조작

~~~shell
#두 곳에 복사
jetson@jp4612GCv346Py37:~/catkin_ws$ cp ~/.ros/automove.txt ~/catkin_ws/src/jessiarm/jessiarm_control/src
jetson@jp4612GCv346Py37:~/catkin_ws$ cp ~/.ros/automove.txt ~/catkin_ws
~~~

~~~shell
$ cd catkin_ws
$ python src/jessiarm/jessiarm_control/src/auto_move.py
~~~

# blob_control

launch 파일로 실행

~~~shell
$ roslaunch jessiarm_control blob_control.launch
~~~

find_ball.yaml 파일에서 인식 색 변경 가능

~~~yaml
#find_ball.yaml의 내용
define: &blue_min [55,40,0]
define: &blue_max [150, 255, 255]
define: &white_min [16, 0, 66]
define: &white_max [133, 251, 255]
define: &pink_min [135, 41, 95]
define: &pink_max [255, 196, 255]
define: &green_min [39, 81, 71]
define: &green_max [75, 255, 255]
define: &orange_min [7, 109, 50]
define: &orange_max [76, 218, 234]
define: &red_min [0, 94, 92]
define: &red_max [255, 255, 255]

#아래 두 줄으로 필터 선택
blob_detector:
  blob_min: *green_min
  blob_max: *green_max
~~~

JessiArm 이 물체를 탐색하는 속도 변경 가능

~~~yaml
jessiarm_speed:
  SCAN_SPEED: 0.04
~~~

이미지 확인

~~~shell
# image_view 실행
$ rqt_image_view
~~~

# Yolo4 PicknPlace

패키지 설치

darknet_ros 설치

~~~shell
sudo apt-get install -y ros-melodic-image-pipeline
cd ~/catkin_ws/src
git clone --recursive https://github.com/Tossy0423/yolov4-for-darknet_ros.git
~~~

darknet_ros 수정

~~~shell
cd ~/catkin_ws/src/yolov4-for-darknet_ros/darknet_ros
git clone https://github.com/zeta0707/darknet_ros_custom.git
cp -rf darknet_ros_custom/* darknet_ros/
~~~

3. 빌드

~~~shell
cma
#cma 는 전체 빌드 단축 명령어
~~~

darknet_ros 실행

2개의 launch 로 구성되어 있으며, 하나는 카메라로 물체의 좌표를 알아내는 역할을 하고, 하나는 그 정보를 이용해 JessiArm을 작동

~~~shell
#terminal #1, object detect using Yolo_v4
jetson@jp4612GCv346Py37:~$ roslaunch darknet_ros yolo_v4.launch

#terminal #2, camera publish, object x/y -> robot move
jetson@jp4612GCv346Py37:~$ roslaunch jessiarm_control yolo_chase.launch                 
~~~

rqt_image_view 를 이용해 탐색되는 오브젝트를 확인 가능
