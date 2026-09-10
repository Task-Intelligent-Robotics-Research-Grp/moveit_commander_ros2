![GitHub release (latest by date)](https://img.shields.io/github/v/release/Task-Intelligent-Robotics-Research-Grp/moveit_commander_ros2)
![GitHub](https://img.shields.io/github/license/Task-Intelligent-Robotics-Research-Grp/moveit_commander_ros2)

| ROS 2 Distribution | Jazzy                                                                                                                                                                    | Lyrical                                                                                                                                                                  |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Build Status       | [![jazzy-build](https://github.com/Task-Intelligent-Robotics-Research-Grp/moveit_commander_ros2/actions/workflows/jazzy-build.yaml/badge.svg)](https://github.com/Task-Intelligent-Robotics-Research-Grp/moveit_commander_ros2/actions/workflows/jazzy-build.yaml) | [![lyrical-build](https://github.com/Task-Intelligent-Robotics-Research-Grp/moveit_commander_ros2/actions/workflows/lyrical-build.yaml/badge.svg)](https://github.com/Task-Intelligent-Robotics-Research-Grp/moveit_commander_ros2/actions/workflows/lyrical-build.yaml) |

# moveit_commander_ros2
Python interface to move_group on MoveiIt2

## オリジナルのmoveit_commander_ros2と本パッケージの修正点
オリジナルの[moveit_commander_ros2](https://github.com/haraisao/moveit_commander_ros2)は，ROS1における[MoveIt](http://docs.ros.org/en/melodic/api/moveit_tutorials/html/index.html)のPythonインタフェースである[moveit_commander](http://docs.ros.org/en/noetic/api/moveit_commander/html/index.html)を，[原功氏](https://hara-jp.com/_default/ja/Home/index.html)がROS2の[MoveIt2](https://moveit.picknik.ai/main/index.html)に[移植](https://hara-jp.com/_default/ja/Topics/moveit_commander_ros2%E3%81%AE%E3%82%A4%E3%83%B3%E3%82%B9%E3%83%88%E3%83%BC%E3%83%AB.html)されたものです．

本パッケージは，これに以下の修正を加えています．
- [RobotCommander.\_\_init__()](./moveit_commander/moveit_commander/robot.py#L154)の引数`ns`とメンバ変数`self._ns`を削除
- [planning_scene_interface.py](./moveit_commander/moveit_commander/planning_scene_interface.py)のベースとなっているコードが古いように思われたので，[最新のもの](https://github.com/moveit/moveit/blob/master/moveit_commander/src/moveit_commander/planning_scene_interface.py)に置き換え

## moveit_commanderを構成するモジュール
`moveit_commander`は以下の3モジュールから構成されます．それぞれ`MoveIt`(ROS1)に属するC++のクラスをPythonでラップしたものですが，`moveit_commander_ros2`では，ラップする対象を`MoveIt2`のそれに置き換えています．

### 1. RobotCommander
MoveItの[moveit::core::RobotModel](http://docs.ros.org/en/noetic/api/moveit_core/html/classmoveit_1_1core_1_1RobotModel.html)，[planning_scene_monitor::CurrentStateMonitor](http://docs.ros.org/en/noetic/api/moveit_ros_planning/html/classplanning__scene__monitor_1_1CurrentStateMonitor.html)に代わって，MoveIt2の[moveit::core::RobotModel](https://moveit.picknik.ai/main/api/html/classmoveit_1_1core_1_1RobotModel.html), [planning_scene_monitor::CurrentStateMonitor](https://moveit.picknik.ai/main/api/html/classplanning__scene__monitor_1_1CurrentStateMonitor.html)を[メンバ変数](./planning_interface_py/robot_interface/src/wrap_python_robot_interface.cpp#L390-L391)に持つ[RobotInterfacePython ](./planning_interface_py/robot_interface/src/wrap_python_robot_interface.cpp#L58)を作り，それにメンバ関数を追加した上で[ラップ](./planning_interface_py/robot_interface/src/wrap_python_robot_interface.cpp#L400-L428)したもの．

### 2. MoveGroupCommander
[MoveItのMoveGroupInterface](http://docs.ros.org/en/noetic/api/moveit_ros_planning_interface/html/classmoveit_1_1planning__interface_1_1MoveGroupInterface.html)に代わって，[MoveIt2のMoveGroupInterface](https://moveit.picknik.ai/main/api/html/classmoveit_1_1planning__interface_1_1MoveGroupInterface.html)を基底クラスとした[MoveGroupInterfaceWrapper](./planning_interface_py/move_group_interface/src/wrap_python_move_group.cpp#L72)を作り，それを[ラップ](./planning_interface_py/move_group_interface/src/wrap_python_move_group.cpp#L821-L973)したもの．MoveItとは次の点が異なる
- [MoveGroupCommander.limit_max_cartesian_link_speed()](http://docs.ros.org/en/noetic/api/moveit_commander/html/classmoveit__commander_1_1move__group_1_1MoveGroupCommander.html#af91ec1bfee96c5cbfc635f2c9d7bf205)は無い．

### 3. PlanningSceneInterface
[MoveItのPlanningSceneInterface](http://docs.ros.org/en/noetic/api/moveit_ros_planning_interface/html/classmoveit_1_1planning__interface_1_1PlanningSceneInterface.html)に代わって，[MoveIt2のPlanningSceneInterface](https://moveit.picknik.ai/main/api/html/classmoveit_1_1planning__interface_1_1PlanningSceneInterface.html)を基底クラスとした[PlanningSceneInterfaceWrapper](./planning_interface_py/planning_scene_interface/src/wrap_python_planning_scene_interface.cpp#L53)を作り，それを[ラップ](./planning_interface_py/planning_scene_interface/src/wrap_python_planning_scene_interface.cpp#L117-L126)したもの．MoveItとは次の点が異なる
- [PlanningSceneInterface::clear()](http://docs.ros.org/en/noetic/api/moveit_ros_planning_interface/html/classmoveit_1_1planning__interface_1_1PlanningSceneInterface.html#ac451cda5d8939e1ddc9793b607c7f4da) が`MoveIt2`で廃止されたので，それに対応していた[PlanningSceneInterface.clear()](http://docs.ros.org/en/noetic/api/moveit_commander/html/classmoveit__commander_1_1planning__scene__interface_1_1PlanningSceneInterface.html#a4f6d89597864ebda56005cc9354983de)も無くなった．
- [PlanningSceneInteraface::getPlanningSceneMsg()](http://docs.ros.org/en/noetic/api/moveit_ros_planning_interface/html/classmoveit_1_1planning__interface_1_1PlanningSceneInterface.html#a33d1548a85c098dcc4abd31f07f5afa3)が`MoveIt2`で廃止されたので，それに対応していた[PlanningSceneInterface.get_planning_scene()](http://docs.ros.org/en/noetic/api/moveit_commander/html/classmoveit__commander_1_1planning__scene__interface_1_1PlanningSceneInterface.html#ade0040a572efe4019849ad00916e2470)も無くなった．

## APIドキュメント
[ROS1のmoveit_commanderのマニュアル](http://docs.ros.org/en/noetic/api/moveit_commander/html/index.html)に含まれる以下の項目を参照してください．

- [**RobotCommander**](http://docs.ros.org/en/noetic/api/moveit_commander/html/classmoveit__commander_1_1robot_1_1RobotCommander.html)
- [**MoveGroupCommander**](http://docs.ros.org/en/noetic/api/moveit_commander/html/classmoveit__commander_1_1move__group_1_1MoveGroupCommander.html)
- [**PlanningSceneInterface**](http://docs.ros.org/en/noetic/api/moveit_commander/html/classmoveit__commander_1_1planning__scene__interface_1_1PlanningSceneInterface.html)


具体的なAPIの使い方については，[チュートリアル](http://docs.ros.org/en/melodic/api/moveit_tutorials/html/doc/move_group_python_interface/move_group_python_interface_tutorial.html)も参考になります．

## その他のPythonインタフェース
ROS1からROS2への移行に伴い`MoveIt`も`MoveIt2`となりましたが，`moveit_commander`は移植されず，長い間`MoveIt2`にはPythonインタフェースがありませんでした．しかし，現在では`moveit_commander_ros2`の他に，以下のインタフェースが存在します．

- [**pymoveit2**](https://github.com/AndrejOrsula/pymoveit2)：`move_group`サーバのクライアントとして，軌道計画とその実行，シーン情報の取得と操作などをサーバに依頼するためのインタフェースを提供．[MoveGroupInterface (MoveIt2, C++)](https://moveit.picknik.ai/main/api/html/classmoveit_1_1planning__interface_1_1MoveGroupInterface.html)および[PlanningSceneInterface (MoveIt2, C++)](https://moveit.picknik.ai/main/api/html/classmoveit_1_1planning__interface_1_1PlanningSceneInterface.html)と同様の機能をPythonで実装し直したものに近いが，これらをラップしているわけではないので，APIはROS1の`moveit_commander`と異なる．
- [**moveit_py**](https://moveit.picknik.ai/main/doc/api/python_api/api.html)：[ROS2 Iron](https://docs.ros.org/en/jazzy/Releases/Release-Iron-Irwini.html)から`MoveIt2`に含まれるようになった公式Pythonインタフェース．`move_group`サーバのクライアントではなく，`MoveIt2`の[コアライブラリを直接ラップ](https://moveit.ai/moveit/ros/python/google/2023/02/15/MoveIt-Humble-Release.html)している．そのため，`moveit_py`を利用するアプリケーションは`move_group`サーバを[必要としない](https://hara-jp.com/_default/ja/Topics/moveit_py%E3%82%92%E4%BD%BF%E3%81%86.html)．APIはROS1の`moveit_commander`と異なる．

`pymoveit2`と本パッケージは`move_group`サーバのクライアントとして機能するのに対し，`moveit_py`は`MoveIt2`のコアライブラリに直接アクセスしている点が大きな違いになります．ROS1の`moveit_commander`とはノード間の関係が大きく変わりますので，`moveit_py`が`MoveIt2`の標準的なPythonインタフェースとして今後広く普及するかは，まだ未知数に思われます．
