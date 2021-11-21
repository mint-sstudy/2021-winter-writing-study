3D 로봇 시뮬레이터인 Gazebo를 설치하고 예제를 통해 Gazebo사용법을 알아가는 시간을 가졌습니다.

0. Gazebo 설치

Gazebo를 설치하기 위해서는 Ubuntu의 버전과 ROS 버전을 맞추고 이에 따라 설치를 해야 한다.

- Ubuntu : 18.04
- ROS : Melodic

먼저 ROS Melodic 버전을 설치하고 제대로 작동이 잘 되는지 Turtle SIM을 확인해보았다.

![1118](https://user-images.githubusercontent.com/70934572/142747993-269de9a5-d418-4b87-aa31-68e784cc7bcf.PNG)

ROS Melodic 버전의 Gazebo 설치 시 명령어

```bash
sudo apt-get install ros-melodic-gazebo-ros
```

## 1. Folder 만들기

Gazebo를 설치하면 ~/.gazebo 가 생기는데 이때, ~/.gazebo/Models 을 만들고, 해당 폴더로 이동하여 test라는 이름의 로봇을 만든다.

```bash
cd ~
mkdir -p .gazebo/models/test
cd .gazebo/models/test/
```

## 2. config 만들기

```bash
<?xml version="1.0"?>
   <model>
       <name>test</name>
       <version>1.0</version>
       <sdf version='1.4'>model.sdf</sdf>
       <author>
           <name>es</name>
           <email>*@gmail.com</email>
       </author>
       <description>project test</description>
</model>
```

- name : Gazebo에 표시될 모델 이름
- sdf : File명

(참고 : [http://gazebosim.org/tutorials?tut=model_structure](http://gazebosim.org/tutorials?tut=model_structure))

## 3. sdf 파일 만들기

3에서 쓴 sdf 파일인 model.sdf 를 만들어 작성한다.

```bash
gedit model.sdf
```

```bash
<?xml version='1.0'?>
<sdf version='1.4'>
 <model name="test"> 
  <static>false</static>

  <link name='link0'> 
   <pose>0 0 0.05 0 0 0</pose> 
        <collision name='collision'> 
    <geometry> 
          <box>
      <size>.1 .1 .1</size>
          </box> 
    </geometry>
   </collision>
   <visual name='visual'>  
    <geometry>
     <box>
      <size>.1 .1 .1</size>
     </box>
    </geometry>
   </visual>
  </link>

  <link name="link1">
   <pose>0 0.1 0.125 0 0 0</pose>
   
   <collision name="collision">
    <geometry>
     <box>
      <size>.05 .3 .05</size>
     </box>
    </geometry>
   </collision>
   <visual name="visual">
    <geometry>
     <box>
      <size>.05 .3 .05</size>
     </box>
    </geometry>
   </visual>
  </link>

  <joint type="revolute" name="joint0">
   <pose>0 0 0 0 0 0</pose>
   <child>link0</child>
   <parent>world</parent>
   <axis>
    <xyz>0 0 0</xyz>
   </axis>
  </joint>

  <joint type="revolute" name="joint1">
   <pose>0 -0.1 0 0 0 0</pose>
   <child>link1</child>
   <parent>link0</parent>
   <axis>
    <limit>
     <lower>-1</lower>
     <upper>1</upper>
    </limit>
    <xyz>0 0 10</xyz>
   </axis>
  </joint>

 </model>
</sdf>
```

(sdf 파일 작성 참고 : [http://sdformat.org/spec](http://sdformat.org/spec))

## 4. World 파일 만들기

World 파일은 Model.config와 Plugin을 연결하는 역할이다.

World를 먼저 생성한다.

```bash
cd  ~
mkdir  world01
cd  world01
gedit  world01.world
```

ground_plane 으로 땅을 만들고, sun 으로 태양을 만들어 그 위에 test 로봇을 올린다.

model 경로 : /usr/share/gazebo-9/model   |  ~/.gazebo/models

```bash
<?xml version="1.0"?>
<sdf version="1.4">
 <world name="default">

 <include> <uri>model://ground_plane</uri> </include>
 <include> <uri>model://sun</uri> </include>
 <include>
    <uri>model://test</uri>
 </include>

 </world>
</sdf>
```

World 를 실행한다.

```bash
gazebo  world01.world
```

## 5. plugin 만들기

plugin이 동작하기 위한 3가지 파일은 다음과 같다.

- .cc : C++ 형태의 PlugIn의 동작이 들어있는 파일
- CMakeLists.txt : .cc 파일을 컴파일 하기 위한 정보가 있는 파일
- .world : 5에서 만든 파일

1) .cc 파일 생성

```bash
cd  ~/world01
gedit  test_spin.cc
```

```bash
#include <boost/bind.hpp>
#include <gazebo/gazebo.hh>
#include <gazebo/physics/physics.hh>
#include <gazebo/common/common.hh>
#include <stdio.h>

namespace gazebo
{
 class testSpin : public ModelPlugin
 {
  public: void init(){}
  public: void reset(){}
  public: void Load(physics::ModelPtr _parent, sdf::ElementPtr)
  {
   this->model = _parent;# sdf와 plugin 이 연결
   joint1 = this->model->GetJoint("joint1");
   this->updateConnection = event::Events::ConnectWorldUpdateBegin(
    boost::bind(&testSpin::OnUpdate, this, _1));
   this->joint1_controller = new physics::JointController(model);
   direction = 1;
   time = 0;

  std::cout << "test" << std::endl;
  }

  public: void OnUpdate(const common::UpdateInfo &)
  {
   time += 1;
   if(time >= 1000){
    time = 0;
    if(direction == 1){
     direction = -1;
     angle = 1.0;
    }
    else{
     direction = 1;
     angle = -1.0;
    }
    joint1_controller->SetJointPosition(joint1, angle);
   std::cout << "test" << std::endl;
   }
  }
  private: physics::ModelPtr model;
  private: physics::JointPtr joint1;
  private: physics::JointController * joint1_controller;
  private: event::ConnectionPtr updateConnection;
  private: int time;
  private: int direction;
  private: double angle;
 };
 GZ_REGISTER_MODEL_PLUGIN(testSpin)
}
```

2) CMakeLists.txt 만들기

```bash
gedit CMakeLists.txt
```

```bash
cmake_minimum_required(VERSION 2.8 FATAL_ERROR)

find_package(Boost REQUIRED COMPONENTS system)
include_directories(${Boost_INCLUDE_DIRS})
link_directories(${Boost_LIBRARY_DIRS})

find_package(gazebo REQUIRED) 
include (FindPkgConfig)
if (PKG_CONFIG_FOUND)
 pkg_check_modules(GAZEBO gazebo)
endif()
include_directories(${GAZEBO_INCLUDE_DIRS})
link_directories(${GAZEOB_LIBRARY_DIRS})

add_library(test_spin SHARED test_spin.cc)
target_link_libraries(ganzang_spin ${GAZEBO_LIBRARIES} ${Boost_LIBRARIES})
```

3) world 파일에 plugin 추가

```bash
<?xml version="1.0"?>
<sdf version="1.4">
 <world name="default">

 <include> <uri>model://ground_plane</uri> </include>
 <include> <uri>model://sun</uri> </include>
 <include>
    <uri>model://test</uri><!-- model.config에 있는 name -->
 </include>

 </world>
</sdf>
```

4) 컴파일 실행

```bash
mkdir  build
cd  build
cmake  ../
make
```

5) 실행

```bash
cd  ..
gazebo  -u  world01.world
```
