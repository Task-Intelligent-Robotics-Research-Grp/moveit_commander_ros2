# moveit_commander_ros2
Python interface to move_group on MoveiIt2

## オリジナルのmoveit_commander_ros2と本パッケージの修正点
オリジナルの[moveit_commander_ros2](https://github.com/haraisao/moveit_commander_ros2)は，ROS1における[MoveIt](http://docs.ros.org/en/melodic/api/moveit_tutorials/html/index.html)のPythonインタフェースである[moveit_commander](http://docs.ros.org/en/noetic/api/moveit_commander/html/index.html)を，[原功氏](https://hara-jp.com/_default/ja/Home/index.html)がROS2の[MoveIt2](https://moveit.picknik.ai/main/index.html)に[移植](https://hara-jp.com/_default/ja/Topics/moveit_commander_ros2%E3%81%AE%E3%82%A4%E3%83%B3%E3%82%B9%E3%83%88%E3%83%BC%E3%83%AB.html)されたものです．

本パッケージは，これに以下の修正を加えています．
- [RobotCommander.\_\_init__()](./moveit_commander/moveit_commander/robot.py#L154)の引数`ns`とメンバ変数`self._ns`を削除
- [planning_scene_interface.py](./moveit_commander/moveit_commander/planning_scene_interface.py)のベースとなっているコードが古いように思われたので，[最新のもの](https://github.com/moveit/moveit/blob/master/moveit_commander/src/moveit_commander/planning_scene_interface.py)に置き換え

## API
`moveit_commander`は，`MoveIt`(ROS1)の`moveit::planning_interface`に属するC++のクラスをPythonでラップしたものですが，`moveit_commander_ros2`では，ラップする対象を`MoveIt2`のそれに置き換えています．しかし，APIはほとんど同じですので，以下の[ROS1のmoveit_commanderのマニュアル](http://docs.ros.org/en/noetic/api/moveit_commander/html/index.html)を参照してください．
- [**RobotCommander (Python)**](http://docs.ros.org/en/noetic/api/moveit_commander/html/classmoveit__commander_1_1robot_1_1RobotCommander.html)：ROS1の[moveit::core::RobotModel](http://docs.ros.org/en/noetic/api/moveit_core/html/classmoveit_1_1core_1_1RobotModel.html)，[planning_scene_monitor::CurrentStateMonitor](http://docs.ros.org/en/noetic/api/moveit_ros_planning/html/classplanning__scene__monitor_1_1PlanningSceneMonitor.html)の代わりに，ROS2の[moveit::core::RobotModel](https://moveit.picknik.ai/main/api/html/classmoveit_1_1core_1_1RobotModel.html), [planning_scene_monitor::CurrentStateMonitor](https://moveit.picknik.ai/main/api/html/classplanning__scene__monitor_1_1CurrentStateMonitor.html)をラップしたもの．
- [**MoveGroupCommander (Python)**](http://docs.ros.org/en/noetic/api/moveit_commander/html/classmoveit__commander_1_1move__group_1_1MoveGroupCommander.html)：ROS1の[MoveGroupInterface (MoveIt, C++)](http://docs.ros.org/en/noetic/api/moveit_ros_planning_interface/html/classmoveit_1_1planning__interface_1_1MoveGroupInterface.html)の代わりに，ROS2の[MoveGroupInterface (MoveIt2, C++)](https://moveit.picknik.ai/main/api/html/classmoveit_1_1planning__interface_1_1MoveGroupInterface.html)をラップしたもの．ROS1とは次の点が異なる
  - [MoveGroupCommander.limit_max_cartesian_link_speed()](http://docs.ros.org/en/noetic/api/moveit_commander/html/classmoveit__commander_1_1move__group_1_1MoveGroupCommander.html#af91ec1bfee96c5cbfc635f2c9d7bf205)は無い．
- [**PlanningSceneInterface (Python)**](http://docs.ros.org/en/noetic/api/moveit_commander/html/classmoveit__commander_1_1planning__scene__interface_1_1PlanningSceneInterface.html)：ROS1の[PlanningSceneInterface (MoveIt, C++)](http://docs.ros.org/en/noetic/api/moveit_ros_planning_interface/html/classmoveit_1_1planning__interface_1_1PlanningSceneInterface.html)の代わりに，ROS2の[PlanningSceneInterface (MoveIt2, C++)](https://moveit.picknik.ai/main/api/html/classmoveit_1_1planning__interface_1_1PlanningSceneInterface.html)をラップしたもの．ROS1とは次の点が異なる
  - C++の[PlanningSceneInterface::clear()](http://docs.ros.org/en/noetic/api/moveit_ros_planning_interface/html/classmoveit_1_1planning__interface_1_1PlanningSceneInterface.html#ac451cda5d8939e1ddc9793b607c7f4da) が`MoveIt2`で消えたので，それに対応していた[PlanningSceneInterface.clear()](http://docs.ros.org/en/noetic/api/moveit_commander/html/classmoveit__commander_1_1planning__scene__interface_1_1PlanningSceneInterface.html#a4f6d89597864ebda56005cc9354983de)も無くなった．
  - C++の[PlanningSceneInteraface::getPlanningSceneMsg()](http://docs.ros.org/en/noetic/api/moveit_ros_planning_interface/html/classmoveit_1_1planning__interface_1_1PlanningSceneInterface.html#a33d1548a85c098dcc4abd31f07f5afa3)が`MoveIt2`で消えたので，それに対応していた[PlanningSceneInterface.get_planning_scene()](http://docs.ros.org/en/noetic/api/moveit_commander/html/classmoveit__commander_1_1planning__scene__interface_1_1PlanningSceneInterface.html#ade0040a572efe4019849ad00916e2470)も無くなった．

具体的なAPIの使い方については，[チュートリアル](http://docs.ros.org/en/melodic/api/moveit_tutorials/html/doc/move_group_python_interface/move_group_python_interface_tutorial.html)も参考になります．

## MoveIt2のためのその他のPythonインタフェース
ROS1からROS2への移行に伴い`MoveIt`も`MoveIt2`となりましたが，`moveit_commander`は移植されず，長い間`MoveIt2`にはPythonインタフェースがありませんでした．しかし，現在では`moveit_commander_ros2`の他に，以下のインタフェースが存在します．

- [**pymoveit2**](https://github.com/AndrejOrsula/pymoveit2)：`move_group`サーバのクライアントとして，軌道計画とその実行，シーン情報の取得と操作などをサーバに依頼するためのインタフェースを提供．[MoveGroupInterface (MoveIt2, C++)](https://moveit.picknik.ai/main/api/html/classmoveit_1_1planning__interface_1_1MoveGroupInterface.html)および[PlanningSceneInterface (MoveIt2, C++)](https://moveit.picknik.ai/main/api/html/classmoveit_1_1planning__interface_1_1PlanningSceneInterface.html)と同様の機能をPythonで実装し直したものに近いが，これらをラップしているわけではないので，APIはROS1の`moveit_commander`と異なる．
- [**moveit_py**](https://moveit.picknik.ai/main/doc/api/python_api/api.html)：[ROS2 Iron](https://docs.ros.org/en/jazzy/Releases/Release-Iron-Irwini.html)から`MoveIt2`に含まれるようになった公式Pythonインタフェース．`move_group`サーバのクライアントではなく，`MoveIt2`の[コアライブラリを直接ラップ](https://moveit.ai/moveit/ros/python/google/2023/02/15/MoveIt-Humble-Release.html)している（[ここ](https://hara-jp.com/_default/ja/Topics/moveit_py%E3%82%92%E4%BD%BF%E3%81%86.html)も参照）．そのため，`moveit_py`を利用するアプリケーションは`move_group`サーバを必要としない．APIはROS1の`moveit_commander`と異なる．

`pymoveit2`と本パッケージは`move_group`サーバのクライアントとして機能するのに対し，`moveit_py`は`MoveIt2`のコアライブラリに直接アクセスしている点が大きな違いになります．ROS1の`moveit_commander`とはノード間の関係が大きく変わりますので，`moveit_py`が`MoveIt2`の標準的なPythonインタフェースとして今後広く普及するかは，まだ未知数に思われます．

