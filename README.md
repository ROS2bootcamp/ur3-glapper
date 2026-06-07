#
---

# 🤖 UR3 + Robotiq 2F-85 Integration

본 프로젝트에서는 기본 UR3 모델에 Robotiq 2F-85 그리퍼를 추가하여 RViz 환경에서 모델을 확인하고 관절 동작을 테스트할 수 있도록 구성하였다.

## 추가 설치 패키지

```bash
sudo apt install -y \
  ros-humble-robotiq-description \
  ros-humble-robot-state-publisher \
  ros-humble-joint-state-publisher \
  ros-humble-joint-state-publisher-gui \
  ros-humble-rviz2
```

---

## 빌드

저장소를 클론한 후 워크스페이스 루트에서 빌드를 진행한다.

```bash
source /opt/ros/humble/setup.bash

colcon build --symlink-install

source install/setup.bash
```

---

## ROS Domain ID 설정

모든 터미널에서 동일한 Domain ID를 사용해야 한다.

```bash
export ROS_DOMAIN_ID=212
```

영구 적용:

```bash
echo 'export ROS_DOMAIN_ID=212' >> ~/.bashrc
source ~/.bashrc
```

---

## RViz 실행

### Terminal 1

URDF 생성 및 Robot State Publisher 실행

```bash
source /opt/ros/humble/setup.bash
source install/setup.bash

export ROS_DOMAIN_ID=212

xacro src/ur3_robotiq_2f85/urdf/ur3_robotiq_2f85.urdf.xacro > /tmp/ur3_robotiq.urdf

ros2 run robot_state_publisher robot_state_publisher \
/tmp/ur3_robotiq.urdf
```

---

### Terminal 2

Joint State Publisher 실행

```bash
source /opt/ros/humble/setup.bash
source install/setup.bash

export ROS_DOMAIN_ID=212

ros2 run joint_state_publisher_gui joint_state_publisher_gui
```

---

### Terminal 3

RViz 실행

```bash
source /opt/ros/humble/setup.bash
source install/setup.bash

export ROS_DOMAIN_ID=212

rviz2
```

RViz 설정:

```text
Fixed Frame : world

Add
 └── RobotModel

Description Topic
 └── /robot_description
```

---

## 실행 결과

정상적으로 실행되면 다음 항목을 확인할 수 있다.

* UR3 로봇팔 표시
* Robotiq 2F-85 그리퍼 표시
* Joint State Publisher GUI를 통한 관절 조작
* RViz 상에서 실시간 로봇 자세 변경 확인

---

# Gripper Service Control

ROS2 Service를 이용하여 Robotiq 2F-85 그리퍼를 열고 닫을 수 있는 기능 확인

## Build

```bash
colcon build --packages-select gripper_service
source install/setup.bash
```

## Run

### Terminal 1

```bash
source /opt/ros/humble/setup.bash
source install/setup.bash

xacro src/ur3_robotiq_2f85/urdf/ur3_robotiq_2f85.urdf.xacro > /tmp/ur3_robotiq.urdf

ros2 run robot_state_publisher robot_state_publisher \
/tmp/ur3_robotiq.urdf
```

### Terminal 2

```bash
source /opt/ros/humble/setup.bash
source install/setup.bash

rviz2
```

### Terminal 3

```bash
source /opt/ros/humble/setup.bash
source install/setup.bash

ros2 run gripper_service gripper_arm_server
```

## Open Gripper

```bash
ros2 service call /gripper_open std_srvs/srv/Trigger
```

## Close Gripper

```bash
ros2 service call /gripper_close std_srvs/srv/Trigger
```

## Available Services

```bash
ros2 service list
```

Expected:

```text
/gripper_open
/gripper_close
```

## Result

* Service 기반 그리퍼 제어
* RViz 실시간 시각화
* ROS2 Topic / Service 통신 학습

## 현재 구현 상태

| 기능               | 상태 |
| ---------------- | -- |
| UR3 모델 로드        | ✅  |
| Robotiq 2F-85 결합 | ✅  |
| RViz 시각화         | ✅  |
| 관절 조작 (GUI)         | ✅  |
| Arm service 제어       | ✅  |
| Gazebo Spawn     | ⏳  |
| ros2_control     | ⏳  |
| MoveIt 연동        | ⏳  |
| Pick & Place     | ⏳  |
