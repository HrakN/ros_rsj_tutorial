---
title: 画像処理とOpenCVの利用
date: 2017-06-01
---

本セクションでは、前セクションで取得した画像を処理する方法について説明します。特にOpenCVを用いて処理する方法について説明します。

本セクションでは、ブロックを見つけ、その位置（Ｘ座標とＹ座標）を出力する一連の処理について説明します。

# OpenCV

OpenCV（Open Source Computer Vision Library）は無料の画像処理ライブラリーです。Linuzの他、WiondowsやMacOSでも利用することができ、現在、多くの画像処理研究で利用されてます。例えば、OpenCVを利用することで、従来手法との精度比較を簡単に行うことができます。

ROSでOpenCVを利用するときの注意点としては、バージョン管理があります。ROSがリリースされたときの最新バージョンのOpenCVを使用することになります。ROSのバージョンとOpenCVのバージョンの対応表をまとめておきます。本セミナーはROS16.04を使用しているため、OpenCV3を利用することになります。

|ROSのバージョン|OpenCVのバージョン|
|17.04 (Lunar Loggerhead)|3|
|16.04 (Kinetic Kame)|3|
|15.04 (Jade Turtle)|2|
|14.04 (Indigo Igloo)|2.4.8|

OpenCVはバージョンが変わると、記述方法や機能が大幅に変更されます。例えば、2から3へバージョンが変わったときは、KNNなどの画像処理が追加されましたが、フレーム間差分などの画像処理などはcontribなどの追加パッケージへ移動されました。





# 事前準備

本セミナーではパッケージ『cv_bridge』を利用します。このパッケージはROSの画像データ（Image Message）とOpenCVの画像データ（IplImage）を相互に変換することができます。つまり、IplImageへ変換し、処理を施し、Image Messageへ戻すという一連の処理を記述することができます。

```shell
sudo apt-get install ros-kinetic-cv-camera
```
IplはIntel Image Processing Libraryの略で、バージョン1で使用されている型になります。そのため、本セミナーでは更にIplImageをMatへ変換します。

# セミナー用画像処理パッケージの作成

新しいワークスペースを作成します。

```shell
$ mkdir -p ~/block_finder_ws/src/
$ cd ~/block_finder_ws/src/
$ catkin_init_workspace
$　ls
CMakeLists.txt
```

次にセミナー用画像処理のROSパッケージをダウンロードします。

```shell
$ git clone git@github.com:Suzuki1984/rsj_2017_block_finder.git

stl-ws2017@stl-ws2017:~/block_finder_ws/src$ ls
CMakeLists.txt  rsj_2017_block_finder
```

コンパイルします。エラーが出ず、[100%]となることを確認します。

```shell
$ cd ~/block_finder_ws/
$ catkin_make 
```

ワークスペース内のパッケージが利用できるようにワークスペースをソースします。

```shell
$ source devel/setup.bash
```

これでセミナー用画像処理のパッケージ「rsj_2017_block_finder」が利用可能になりました。

# セミナー用画像処理パッケージの内容

セミナー用画像処理パッケージの内容を確認します。

```shell
$ ls
CMakeLists.txt  launch  package.xml  readme.md  rsj_2017_block_finder.rviz  src
```

ディレクト「launch」の中にはblock_finder.launchがあります。このlaunchファイルでは4つのノードを起動します。配信（publish）と購読（listen）の関係性を以下に示します。

```shell
<?xml version="1.0"?>
<launch>
 <node pkg="usb_cam" type="usb_cam_node" name="usb_cam_node" output="screen">
  <param name="image_width" value="640"/>
  <param name="image_height" value="480"/>
  <param name="pixel_format" value="yuyv"/>
  <param name="camera_frame_id" value="camera_link"/>
 </node>
 <node pkg="rsj_2017_block_finder" type="block_finder" name="block_finder" output="screen"/>
 <node pkg="tf" type="static_transform_publisher" name="camera_transform_publisher" args="-0.118 -0.039 0.474 -0.293 -0.075 -0.191 0.934 /camera_link /world 100"/>
 <node pkg="rviz" type="rviz" name="rviz" args="-d $(find rsj_2017_block_finder)/rsj_2017_block_finder.rviz"/>
</launch>
```

`usb_cam_node`
: 画像メッセージを配信する。

`block_finder`
: 画像メッセージを購読し、処理し、世界座標系におけるブロックの位置を配信する。

`camera_transform_publisher`
: 世界座標系の原点から見たカメラ座標系の原点の位置を配信する。

`rviz`
: 世界座標、カメ座標系、ブロックの位置関係を確認する。

# セミナー用画像処理プログラムの内容

カメラを接続し、チェスボードを机の上に置いたあと、下記のコマンドで実行します。入力画像、出力画像、RVizの３つの画面が開きます。チェスボード上に黄色の四角形が表示されていれば正常に起動しています。

```shell
$ roslaunch rsj_2017_block_finder block_finder.launch
```

![Block Finder GUI](images/bf01.png)

次に、チェスボードを退かし、黄色の四角形に収まるようにブロックを置きます。

TFは座標系を表示し、R色がX軸、G色がY軸、B色がZ軸を表します。

PointStampedはHeaderとPointが組み合わさったメッセージで、Headerで位置データを取得した時刻、Pointで位置データを表現することができます。




# ほげほげ






次にMoveIt!を起動します。新しい端末で以下を実行します。

```shell
$ cd ~/crane_plus_ws/
$ source devel/setup.bash
$ roslaunch crane_plus_moveit_config move_group.launch
... logging to /home/username/.ros/log/7b527712-3aa3-11e7-b868-d8cb8ae35bff/roslaunch-alnilam-3483.log
Checking log directory for disk usage. This may take awhile.
Press Ctrl-C to interrupt
Done checking log file disk usage. Usage is <1GB.

started roslaunch server http://alnilam:33499/

SUMMARY
（省略）
[ INFO] [1494986092.617635076, 141.869000000]: MoveGroup context initialization complete

You can start planning now!
```

最後に「You can start planning now!」が出力されたら、マニピュレータは利用可能な状態になりました。

MoveIt!は、ROSノードでMoveIt!のAPIを利用することが基本の利用方法です。しかし、試すだけのためにノードを作成することは重い作業です。ノード作成の代わりにROSの基本の可視化ツール[RViz](http://wiki.ros.org/rviz)も利用できます。

MoveIt!はRViz上でマニピュレータ制御ユーザインターフェースをプラグインとして提供します。MoveIt!と同時にインストールされて、CRANE+のMoveIt!パッケージから起動します。新しい端末で以下を実行してRVizのMoveIt!ユーザインターフェースを起動します。

```shell
$ cd ~/crane_plus_ws/
$ source devel/setup.bash
$ roslaunch crane_plus_moveit_config moveit_rviz.launch config:=true
```

![MoveIt! RViz interface](images/crane_plus_moveit_rviz_panel_hardware.png)

__注意：コマンドの最後に`config:=true`を忘れると、上記画像のようなGUIが表示されません。__{:style="color: red" }

RVizでカメラの制御は以下で行います。

マウスをクリックとドラッグ
: 青い点を中心にしてカメラの回転

__Shift__{: style="border: 1px solid black" } を押しながらマウスをクリックとドラッグ　または　マウスをミドルクリックとドラッグ
: 青い点を中心にしてカメラをXYで移動する

マウスウィール　または　マウス右クリックとドラッグ
: 青い点を中心にしてカメラズーム

RViz内の「Motion Planning」パネルにある「Planning」タブをクリックして、以下のインターフェースを開きます。

![MoveIt! RViz interface](images/crane_plus_moveit_rviz_panel_planning_tab_hardware_labeled.png)

最初のテストとして、マニピュレータをランダムな姿勢に移動しましょう。Planningタブ内の「Select Goal State」で「`<random valid>`」が選択されているを確認して、「Update」をクリックします。

![MoveIt! RViz random pose](images/crane_plus_moveit_rviz_random_pose_hardware.png)

__注意：本物のロボットを制御します。ランダムで選択された姿勢は机等に当たらないようになるまでに、「Update」をクリックしましょう。__{:style="color: red" }

安全な姿勢になったら、「Plan」をクリックします。MoveIt!が移動プランを計算します。RVizでロボットが追う経路は表示されます。

![MoveIt! RViz random pose plan](images/crane_plus_moveit_rviz_random_pose_plan_hardware.png)

プランを実行します。「Execute」をクリックするとシミュレータ上のマニピュレータが指定した姿勢に移動します。RViz上でロボットの現在の姿勢が表示され、これも指定したポーズ（すなわちシミュレータ上のロボットのポーズ）に移動します。

![MoveIt! RViz random pose execution](images/crane_plus_moveit_rviz_random_pose_executed_hardware.png)

ランダムな姿勢に移動できたら、次に手動制御を行いましょう。RViz内でマニピュレータの先端に球体と丸と矢印があります。以下の方法でこれらを利用してグリッパーの位置と角度が制御できます。

球体をドラッグ
: グリッパーの位置を移動する。

丸を回す
: グリッパーの角度を変更する。（注意：CRANE+は4DOFのマニピュレータだけなのでグリッパーの角度は上下（緑色の丸）しか変更できない。他の角度変更は無視される。）

矢印をドラッグ
: グリッパーの位置を一つの軸だけで移動する。

基本的にMoveIt!のユーザインターフェースは可能か姿勢しか許さないので、時々マニピュレータは移動してくれないことや飛ぶことがあります。

お好みの姿勢にグリッパーを移動して、「Plan」と「Execute」ボタンでマニピュレータを移動しましょう。シミュレータ上のロボットはグリッパーが指定した位置と角度になるように動きます。

## ノードからマニピュレータを制御

MoveIt!を利用するために、主にノードからアプリケーションやタスクに沿ったようにマニピュレータを制御したいでしょう。ここで簡単なノードの作成によりグリッパーの位置と角度を制御します。

### ノードを作成

最初にワークスペースにノード用の新しいパッケージを作成します。

```shell
$ cd ~/crane_plus_ws/src/
$ catkin_create_pkg pick_and_placer roscpp moveit_core moveit_ros_planning_interface moveit_visual_tools \
    moveit_msgs moveit_commander tf actionlib control_msgs geometry_msgs shape_msgs trajectory_msgs
Created file pick_and_placer/CMakeLists.txt
Created file pick_and_placer/package.xml
Created folder pick_and_placer/include/pick_and_placer
Created folder pick_and_placer/src
Successfully created files in /home/username/crane_plus_ws/src/pick_and_placer.
    Please adjust the values in package.xml.
```

パッケージ内の`package.xml`の依存関係は以下のようになるように編集します。

```xml
  <buildtool_depend>catkin</buildtool_depend>

  <build_depend>roscpp</build_depend>
  <build_depend>moveit_core</build_depend>
  <build_depend>moveit_ros_planning_interface</build_depend>
  <build_depend>moveit_visual_tools</build_depend>
  <build_depend>control_msgs</build_depend>
  <build_depend>actionlib</build_depend>
  <build_depend>geometry_msgs</build_depend>
  <build_depend>shape_msgs</build_depend>

  <run_depend>roscpp</run_depend>
  <run_depend>rospy</run_depend>
  <run_depend>moveit_core</run_depend>
  <run_depend>moveit_ros_planning_interface</run_depend>
  <run_depend>moveit_visual_tools</run_depend>
  <run_depend>tf</run_depend>
  <run_depend>geometry_msgs</run_depend>
  <run_depend>shape_msgs</run_depend>
  <run_depend>control_msgs</run_depend>
  <run_depend>actionlib</run_depend>
  <run_depend>trajectory_msgs</run_depend>
  <run_depend>moveit_msgs</run_depend>
  <run_depend>moveit_commander</run_depend>
```

パッケージ内の`CMakeLists.txt`にある`find_package`コマンドは以下の用に編集し依存関係パッケージを利用します。

```cmake
find_package(catkin REQUIRED COMPONENTS
  roscpp
  moveit_core
  moveit_ros_planning
  moveit_ros_planning_interface
  moveit_visual_tools
  control_msgs
  geometry_msgs
  shape_msgs
  actionlib
)

find_package(Boost REQUIRED
  system
  filesystem
  date_time
  thread
)
```

`CMakeLists.txt`の`catkin_package`コマンドにも追加します。

```cmake
catkin_package(
  CATKIN_DEPENDS
    roscpp
    control_msgs
    geometry_msgs
    shape_msgs
    moveit_core
    moveit_ros_planning_interface
    actionlib
)
```

`CMakeLists.txt`で`include_directories`にBoostのディレクトリを追加します。

```cmake
include_directories(
  ${catkin_INCLUDE_DIRS}
  ${Boost_INCLUDE_DIR}
)
```

パッケージ内の`CMakeLists.txt`にノード用のコンパイル情報を追加します。ここにもBoostの情報を利用します。

```cmake
## Declare a C++ executable
## With catkin_make all packages are built within a single CMake context
## The recommended prefix ensures that target names across packages don't collide
# add_executable(${PROJECT_NAME}_node src/servo_control_node.cpp)
add_executable(${PROJECT_NAME}_pick_and_placer src/pick_and_placer.cpp)

## Rename C++ executable without prefix
## The above recommended prefix causes long target names, the following renames the
## target back to the shorter version for ease of user use
## e.g. "rosrun someones_pkg node" instead of "rosrun someones_pkg someones_pkg_node"
set_target_properties(${PROJECT_NAME}_pick_and_placer
  PROPERTIES OUTPUT_NAME pick_and_placer PREFIX "")

## Add cmake target dependencies of the executable
## same as for the library above
add_dependencies(${PROJECT_NAME}_pick_and_placer
  ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})

## Specify libraries to link a library or executable target against
target_link_libraries(${PROJECT_NAME}_pick_and_placer
  ${catkin_LIBRARIES}
  ${Boost_LIBRARIES}
)
```

なお、__必ず__{: style="color: red" } ファイルトップに`add_definitions(-std=c++11)`の行をアンコメントしてください。

`pick_and_placer`パッケージ内の`src/`ディレクトリに`pick_and_placer.cpp`というファイルを作成します。そしてエディターで開き、以下のソースを入力します。

```cpp
#include <ros/ros.h>

int main(int argc, char **argv) {
  ros::init(argc, argv, "pickandplacer");
  ros::NodeHandle nh;

  ros::shutdown();
  return 0;
}
```

このソースは空のノードです。これから少しづつMoveIt!のAPIを利用するコードを追加してマニピュレータを制御します。

### マニピュレータを保存された姿勢に移動

最初は、５行目（`ros::NodeHandle nh;`）の後に以下を追加します。MoveIt!はアシンクロナスな計算をしないといけないので、このコードによりROSのアシンクロナスな機能を初期化します。

```c++
  ros::AsyncSpinner spinner(2);
  spinner.start();
```

次はMoveIt!のAPIの初期化です。ファイルの上に以下のヘッダーをインクルードします。

```c++
#include <moveit/move_group_interface/move_group_interface.h>
```

そして`main`関数に以下の変数を追加します。

```c++
  moveit::planning_interface::MoveGroupInterface arm("arm");
```

MoveIt!は「MoveGroup」という存在を制御します。「MoveGroup」とは、ロボット内の複数のジョイントのグループです。CRANE+には２つのMoveGroupがあります。「arm」は手首の部分までのジョイントを制御し、「gripper」は指のジョイントのみを制御します。以下は「arm」のMoveGroupです。

![CRANE+ "arm" MoveGroup](images/crane_plus_move_group_arm.png)

`arm`のMoveGroupの先端はグリッパーの真ん中ぐらいに設定されています。これで`arm`の位置を指定すると、グリッパーはその位置の周りに行きます。

MoveIt!はどの座標系で制御するかを指定することが必要です。今回ロボットのベースに基づいた「`base_link`」座標系を利用します。位置制御の座標等はロボットのベースから図るという意味です。

```c++
  arm.setPoseReferenceFrame("base_link");
```

これでマニピュレータは制御できるようになりました。

最初の動きとして、マニピュレータを立てましょう。MoveIt!は「Named pose」（名付きポーズ）というコンセプトを持ちます。CRANE+のMoveIt!コンフィグレーション（`crane_plus_moveit_config`パッケージにある）は２つの名付きポーズを指定します。

`vertical`
: マニピュレータが真上を指す

`resting`
: マニピュレータは休んでいるような姿勢になる

`vertical`ポーズを利用してマニピュレータを立ちます。

```c++
  arm.setNamedTarget("vertical");
```

移動先を設定した後、MoveIt!にプラン作成を移動命令を出します。

```c++
  arm.move();
```

ノードをコンパイルします。端末で以下を実行します。

```shell
$ catkin_make
Base path: /home/username/crane_plus_ws
Source space: /home/username/crane_plus_ws/src
Build space: /home/username/crane_plus_ws/build
Devel space: /home/username/crane_plus_ws/devel
Install space: /home/username/crane_plus_ws/install
（省略）
[ 85%] Linking CXX executable /home/username/crane_plus_ws/devel/lib/pick_and_placer/pick_and_placer
[ 85%] Built target pick_and_placer_pick_and_placer
[100%] Linking CXX executable /home/username/crane_plus_ws/devel/lib/
         crane_plus_camera_calibration/calibrate_camera_checkerboard
[100%] Built target calibrate_camera_checkerboard
$
```

次にマニピュレータを起動します。

```shell
$ roslaunch crane_plus_hardware start_arm_standalone.launch
... logging to /home/username/.ros/log/4de82534-3e85-11e7-a03f-d8cb8ae35bff/roslaunch-alnilam-27138.log
Checking log directory for disk usage. This may take awhile.
Press Ctrl-C to interrupt
Done checking log file disk usage. Usage is <1GB.

started roslaunch server http://alnilam:37805/
（省略）
[joint_trajectory_controller_spawner-4] process has finished cleanly
[servo_controller_spawner-3] process has finished cleanly
```

そして、MoveIt!を起動します。別の端末で以下のコマンドを実行します。

```shell
$ roslaunch crane_plus_moveit_config move_group.launch
... logging to /home/username/.ros/log/4de82534-3e85-11e7-a03f-d8cb8ae35bff/roslaunch-alnilam-28429.log
Checking log directory for disk usage. This may take awhile.
Press Ctrl-C to interrupt
Done checking log file disk usage. Usage is <1GB.

started roslaunch server http://alnilam:33774/
（省略）
[ INFO] [1495412987.240640847]: MoveGroup context using planning plugin ompl_interface/OMPLPlanner
[ INFO] [1495412987.240652629]: MoveGroup context initialization complete

You can start planning now!
```

最後に、作成したノードを起動します。別の端末で以下を実行します。

```shell
$ rosrun pick_and_placer pick_and_placer
[ INFO] [1495413039.031396268]: Loading robot model 'crane_plus'...
[ INFO] [1495413039.031446316]: No root/virtual joint specified in SRDF. Assuming fixed joint
[ INFO] [1495413040.033491742]: Ready to take commands for planning group arm.
```

成功であれば、マニピュレータは立ちます。

![CRANE+ vertical named pose](images/crane_plus_vertical_pose.png)

_このソースは以下のURLでダウンロード可能です。_

<https://github.com/gbiggs/rsj_2017_pick_and_placer/tree/named_pose>

_編集されたC++ファイルは以下です。_

<https://github.com/gbiggs/rsj_2017_pick_and_placer/blob/named_pose/src/pick_and_placer.cpp>

### マニピュレータを任意の姿勢に移動

MoveIt!によってマニピュレータのグリッパーを任意の姿勢に移動します。

グリッパーの姿勢を指定するために、位置と角度を指定することが必要です。作成したソースから`arm.move()`の行を削除し、以下を追加します。

```c++
  // Prepare
  ROS_INFO("Moving to prepare pose");
  geometry_msgs::PoseStamped pose;
  pose.header.frame_id = "base_link";
  pose.pose.position.x = 0.2;
  pose.pose.position.y = 0.0;
  pose.pose.position.z = 0.1;
  pose.pose.orientation.x = 0.0;
  pose.pose.orientation.y = 0.707106;
  pose.pose.orientation.z = 0.0;
  pose.pose.orientation.w = 0.707106;

  arm.setPoseTarget(pose);
  if (!arm.move()) {
    ROS_WARN("Could not move to prepare pose");
    return 1;
  }
```

上記のソースの前半はグリッパーの姿勢を設定します。`geometry_msgs/PoseStamped`メッセージを利用します：

```
std_msgs/Header header
  uint32 seq
  time stamp
  string frame_id
geometry_msgs/Pose pose
  geometry_msgs/Point position
    float64 x
    float64 y
    float64 z
  geometry_msgs/Quaternion orientation
    float64 x
    float64 y
    float64 z
    float64 w
```

`header`の下の`frame_id`に、このポーズのタスクフレーム（座標系）を指定します。先に指定した制御座標系`base_link`を指定します。

`pose`の下の`position`はグリッパーの位置です。マニピュレータから真っ直ぐ前の20 cm、机から10 cm離れた所にします。

`pose`の下の`orientation`はグリッパーの角度です。ROSでは角度をオイラー角ではなくて、四元数（quaternion）として指定します。人に分かりにくくなりますが、計算としてより楽で早いです。指定している角度は、グリッパーがX軸を指します(机に対して水平になります。)。

もう一度コンパイルして実行します。

```shell
$ catkin_make
Base path: /home/username/crane_plus_ws
Source space: /home/username/crane_plus_ws/src
Build space: /home/username/crane_plus_ws/build
Devel space: /home/username/crane_plus_ws/devel
Install space: /home/username/crane_plus_ws/install
（省略）
[ 85%] Linking CXX executable /home/username/crane_plus_ws/devel/lib/pick_and_placer/pick_and_placer
[ 85%] Built target pick_and_placer_pick_and_placer
[100%] Linking CXX executable /home/username/crane_plus_ws/devel/lib/
         crane_plus_camera_calibration/calibrate_camera_checkerboard
[100%] Built target calibrate_camera_checkerboard
$ rosrun  pick_and_placer pick_and_placer
[ INFO] [1495423076.668768146]: Loading robot model 'crane_plus'...
[ INFO] [1495423076.668847786]: No root/virtual joint specified in SRDF. Assuming fixed joint
[ INFO] [1495423077.846839325]: Ready to take commands for planning group arm.
$
```

![CRANE+ pick pre-grasp pose](images/crane_plus_pick_pre_grasp_pose.png)

_このソースは以下のURLでダウンロード可能です。_

<https://github.com/gbiggs/rsj_2017_pick_and_placer/tree/specified_pose>

_編集されたC++ファイルは以下です。_

<https://github.com/gbiggs/rsj_2017_pick_and_placer/blob/specified_pose/src/pick_and_placer.cpp>

## グリッパーを開く

MoveIt!はグリッパーの制御もできるが、直接グリッパーコントローラにコマンドを出すこともよくあります。こちらでは後方の方法でグリッパーの開けく・閉めることを行います。

グリッパーコントローラはROSの`actionlib`（アシンクロナスRPCのような構造）を利用し、`control_msgs/GripperCommandAction`アクションを利用します。このアクションのリクエストは`control_msgs/GripperCommandGoal`で、内容は以下です。

```
control_msgs/GripperCommand command
  float64 position
  float64 max_effort
```

上記のメッセージの中にある`position`はグリッパーの指の幅を示します。ゼロにするとグリッパーは完全に閉じられます。開いた状態の幅はもちろんグリッパーによって変わります。CRANE+の場合は、10 cm 程度です。すなわち、グリッパーが開けたい時に`position`を`0.1`に設定し、閉じたい時に`position`を`0`に設定します。正しい、何かを持ちたい場合はゼロに設定すると __グリッパーサーボがストールしエラーになる可能性や持つものを壊す可能性がある__ ので、普段は持つ物の大きさに合わせます。

アクションの利用はサーバーとクライアントが必要です。CRANE+のグリッパーの場合には、サーバーは`crane_plus_gripper`ノードで、クライントはここで作成するノードです。

ノードのソースを編集してグリッパーを開けましょう。

まずはヘッダーファイルに下記を追加します。

```c++
#include <actionlib/client/simple_action_client.h>
#include <control_msgs/GripperCommandAction.h>
```

つぎにノードの初期化あたりに下記でグリッパーのアクションクリアンとを初期化します。

```c++
  actionlib::SimpleActionClient<control_msgs::GripperCommandAction> gripper(
      "/crane_plus_gripper/gripper_command",
      "true");
  gripper.waitForServer();
```

最後に、マニピュレータを移動したあとに下記を追加してグリッパーを開けます。

```c++
  // Open gripper
  ROS_INFO("Opening gripper");
  control_msgs::GripperCommandGoal goal;
  goal.command.position = 0.1;
  gripper.sendGoal(goal);
  bool finishedBeforeTimeout = gripper.waitForResult(ros::Duration(30));
  if (!finishedBeforeTimeout) {
    ROS_WARN("Gripper open action did not complete");
    return 1;
  }
```

ノードをコンパイルし実行すると以下のようにグリッパーが開きます。

![CRANE+ pick open gripper](images/crane_plus_pick_open_gripper.png)

_このソースは以下のURLでダウンロード可能です。_

<https://github.com/gbiggs/rsj_2017_pick_and_placer/tree/open_gripper>

_編集されたC++ファイルは以下です。_

<https://github.com/gbiggs/rsj_2017_pick_and_placer/blob/open_gripper/src/pick_and_placer.cpp>

## ピッキングタスクを行う

本セクションではマニピュレータを利用して机の上の物を運びます。

上記のマニピュレータを机の上に移動することとグリッパーを開けることは「ピッキング」というタスクの１番目と２番目のステップです。ピッキングタスクの全部を見ると、以下のようになります。

1. アプローチのスタート点に移動

   的の物体の近くに移動して、持つための角度に合わせる [pre-grasp pose]

   ![CRANE+ pick pre-grasp pose](images/crane_plus_pick_step_prepare.jpg)

1. グリッパーを準備する

   物体を持つためにグリッパーを開ける

   ![CRANE+ pick open gripper](images/crane_plus_pick_step_open_gripper.jpg)

1. アプローチを実行する

   グリッパーが物体の周りになるようにアプローチベクターに沿って、グラスプポーズに移動する [approach、grasp pose]

   ![CRANE+ pick grasp pose](images/crane_plus_pick_step_approach.jpg)

1. グリッパーを閉じて物体を持つ

   グリッパーが物体に充分力を与えるまでに閉じる [grasp]

   ![CRANE+ pick close gripper](images/crane_plus_pick_step_close_gripper.jpg)

1. リトリートを実行する

   物体を持ちながらテーブル等から離れる（アプローチの反対方向ではない場合もある） [retreat, post-grasp post]

   ![CRANE+ pick post grasp](images/crane_plus_pick_step_retreat.jpg)

前セクションでステップ１と２を実装しました。次に下記のソースをノードに追加してステップ３から５を実装します。

```c++
  // Approach
  ROS_INFO("Executing approach");
  pose.pose.position.z = 0.05;
  arm.setPoseTarget(pose);
  if (!arm.move()) {
    ROS_WARN("Could not move to grasp pose");
    return 1;
  }

  // Grasp
  ROS_INFO("Grasping object");
  goal.command.position = 0.015;
  gripper.sendGoal(goal);
  finishedBeforeTimeout = gripper.waitForResult(ros::Duration(30));
  if (!finishedBeforeTimeout) {
    ROS_WARN("Gripper close action did not complete");
    return 1;
  }

  // Retreat
  ROS_INFO("Retreating");
  pose.pose.position.z = 0.1;
  arm.setPoseTarget(pose);
  if (!arm.move()) {
    ROS_WARN("Could not move to retreat pose");
    return 1;
  }
```

ノードをコンパイルして実行してみましょう。成功であればマニピュレータはピッキングタスクを行います。

これでROS上でMoveIt!とマニピュレータを利用して、ピック・アンド・プレースの前半が実装できました。

_上述のソースは以下のURLでダウンロード可能です。_

<https://github.com/gbiggs/rsj_2017_pick_and_placer/tree/picking>

_編集されたC++ファイルは以下です。_

<https://github.com/gbiggs/rsj_2017_pick_and_placer/blob/picking/src/pick_and_placer.cpp>

## 小課題

実装したノードはすでに決まっている所（`(x: 0.2, y: 0.0)`）にしかピッキングできません。従来のロボットワークセルでは、決まっている所にタスクを行うことが普通です。しかし、将来の産業ロボットには、そしてサービスロボットにも、センサーデータによって物体の場所を判断して、そしてピッキングします。

上記で実装したノードを、ROSのトピックから受信した場所にあるブロックをピッキングするように変更してみましょう。

トピックのメッセージタイプに`geometry_msgs/Pose2D`は利用できます。

```
float64 x
float64 y
float64 theta
```

`theta`を無視して、`x`と`y`だけでブロックの位置を示す。`geometry_msgs/Pose2D`のヘッダーは`geometry_msgs/Pose2D.h`です。

ノードにブロックの位置を送信するために、`rostopic`が利用できます。例えばトピックの名は`block`であれば、端末で以下を実行するとノードに位置情報が送信できます。

```shell
$ rostopic pub -1 /block geometry_msgs/Pose2D "{x: 0.1, y: 0.0}"
```

ノードはトピックにサブスクライブして、コールバックでピッキングタスクを行います。

ノードの機能をクラスで実装するとき、ROSの初期化はどこにすればいいでしょうか。`main`関数にしても、クラスのコンストラクターのしても構いません。

コールバックは`main`関数にある`arm`や`gripper`の変数にアクセスできません。この場合はグローバル変数にするか、ノードをクラスとして実装するかという２つのオプションがあります。メンテナンスの観点からグローバル変数は利用しない方がいいので、C++が分かるかたはクラスで実装することがおすすめです。

クラスとして実装すると`arm`や`gripper`はクラスのメンバー変数して、クラスメソッドをコールバックとして利用して、そしてコールバックからクラスのメンバー変数である`arm`と`gripper`へアクセスすることが可能になります。

サブスクライバのコールバックにクラスメソッドを利用するとき、`nh.subscribe()`関数へ渡す変数は少し変わります。

```c++
ros::Subscriber sub = node_handle.subscribe("/block", 1, &PickNPlacer::DoPick, this);
```

3番目の変数は代わり、4番目の変数が追加しました。

`&PickNPlace::DoPick'
: コールバックになるクラスメソッドをクラス名（`PickNPlace`）から指定します。

`this`
: `msg`以外のコールバックに渡す変数を指定します。`this`は、クラスへのポインターです。クラスメソッドの最初の変数は実はクラスポインターですが、普段コンパイラーが隠すので見えません。この場合は指定しないとクラスのデータにアクセスできません。

__注意：変更し始める前に、ソースのバックアップを作りましょう。__{: style="color: red" }

_このソースは以下のURLでダウンロード可能です。_

<https://github.com/gbiggs/rsj_2017_pick_and_placer/tree/topic_picker>

## 更に小課題

ピッキングタスクの逆は「place」（プレース、置くこと）です。動きは基本的にピッイングの逆ですが、物体の置き方により動きは代わることがあります。

CRANE+と本セミナーの物体（すなわちスポンジのブロック）の場合は、プレースはピッキングの逆で問題ありません。（落とすことでも問題ありませんが、少し無粋でしょう。）

ノードにピッキング後にプレースを行うソースを実装してみましょう。

__注意：変更し始める前に、ソースのバックアップを作りましょう。__{: style="color: red" }

_このソースは以下のURLでダウンロード可能です。_

<https://github.com/gbiggs/rsj_2017_pick_and_placer/tree/pickandplace>

## 更に小課題

ピックの場所をトピックから受信するようにしたらノードは様々なアプリケーションで再利用できるようになりました。しかし、プレースの場所はまだソースでハードコードしたのでノードの再利用性が低いです。

プレースの場所を簡単に変更してアプリケーションに合わせるノードを作りましょう。プレースの場所をパラメータで設定できるようにします。

パラメータを利用するために、まずはノードがパラメータをロードする機能を追加します。`ros::param::param()`関数を利用します。

ノードのクラスのセットアップするところ（コンストラクタなど）に、最初に`ros::param::param()`を呼びプレース座標を読み込む行を追加します。`arm_`を作成する前に入れます。

```c++
    ros::param::param<geometry_msgs::Pose2D>(
      "~place_position",
      place_position_,
      geometry_msgs::Pose2D(0.1, -0.2, 0.0));
```

１行目は関数を呼びます。テンプレート関数のでパラメータのデータ型を指定します。

２行目でパラメータ名を指定します。パラメータサーバからこのパラメータを読み込みます。`~`で始まる理由は、ノードのネームスペース内のパラメータだと指定します。基本的に一つのノードだけで利用するパラメータはノードのネームスペース内に置くべきです。

３行目は保存先を指定します。メンバー変数`place_position_`に保存します。

４行目はディフォルト値です。パラメータサーバに指定したパラメータがなかったら、この値は`place_position_`に入れられます。

クラスのプライベートな変数も追加します。

```c++
  geometry_msgs::Pose2D place_position_;
```

実装の最後として、`DoPlace()`内で`pose`変数を初期化するところに、`pose.pose.position.x`と`pose.pose.position.y`の値をパラメータからとるように変更します。

```c++
    pose.pose.position.x = place_position_.x;
    pose.pose.position.y = place_position_.y;
```

パラメータの設定は、「ROSの基本操作」で学んだように、ノードの起動時に行います。

```shell
$ rosrun pick_and_placer pick_and_placer _place_position:='{x: 0.1, y: -0.2, theta: 0.0}'
```

下記のように、パラメータをlaunchファイルに指定することも可能です。

```xml
  <node name="pickandplace" pkg="pick_and_placer" type="pick_and_placer" output="screen">
    <param name="place_position" value="{x: 0.1, y: -0.2, theta: 0.0}"/>
  </node>
```

ノードの他の値もパラメータで設定できるようにすると、さらに再利用しやすくなります。例えば、以下をパラメータにする価値があります。

- プランニングを行うタスクフレーム（`setPoseReferenceFrame()`と`frame_id`に利用）
- ピック前ポーズのZ値（机の上の高さ）
- ピックポーズのZ値
- プレース前ポーズのZ値
- プレースポーズのZ値
- グリッパーの開ける値
- グリッパーの閉める値

_このソースは以下のURLでダウンロード可能です。_

<https://github.com/gbiggs/rsj_2017_pick_and_placer/tree/parameterised>

## 更に小課題

MoveIt!はピック・アンド・プレースを行うソースが含まれています。動きを自分で指定するより、この機能を利用するとMoveIt!の様々な機能（コリジョン・チェッカー等）が自動的に利用されます。

ノードのソースを編集して、MoveIt!の「pick」と「place」アクションを利用しましょう。

下記のヘッダーファイルを追加します。

```c++
#include <moveit_msgs/Grasp.h>
```

`DoPick`関数の中身は下記のとおりになります。

```c++
  bool DoPick(geometry_msgs::Pose2D::ConstPtr const& msg) {
    std::vector<moveit_msgs::Grasp> grasps;
    moveit_msgs::Grasp g;
    g.grasp_pose.header.frame_id = arm_.getPlanningFrame();
    g.grasp_pose.pose.position.x = msg->x;
    g.grasp_pose.pose.position.y = msg->y;
    g.grasp_pose.pose.position.z = 0.05;
    g.grasp_pose.pose.orientation.y = 0.707106;
    g.grasp_pose.pose.orientation.w = 0.707106;

    g.pre_grasp_approach.direction.header.frame_id = arm_.getPlanningFrame();
    g.pre_grasp_approach.direction.vector.z = -1;
    g.pre_grasp_approach.min_distance = 0.05;
    g.pre_grasp_approach.desired_distance = 0.07;

    g.post_grasp_retreat.direction.header.frame_id = arm_.getPlanningFrame();
    g.post_grasp_retreat.direction.vector.z = 1;
    g.post_grasp_retreat.min_distance = 0.05;
    g.post_grasp_retreat.desired_distance = 0.07;

    g.pre_grasp_posture.joint_names.resize(1, "crane_plus_moving_finger_joint");
    g.pre_grasp_posture.points.resize(1);
    g.pre_grasp_posture.points[0].positions.resize(1);
    g.pre_grasp_posture.points[0].positions[0] = 0.1;

    g.grasp_posture.joint_names.resize(1, "crane_plus_moving_finger_joint");
    g.grasp_posture.points.resize(1);
    g.grasp_posture.points[0].positions.resize(1);
    g.grasp_posture.points[0].positions[0] = 0.01;

    grasps.push_back(g);
    arm_.setSupportSurfaceName("table");
    ROS_INFO("Beginning pick");
    if (!arm_.pick("sponge", grasps)) {
      ROS_WARN("Pick failed");
      return false;
    }
    ROS_INFO("Pick complete");
    return true;
  }
```

上記は`Grasp`を作成します。物体を持つための位置と角度、アプローチベクター、リトリートベクター及びグリッパーの開けると閉じる方法（直接ジョイント制御で）を指定します。

本方法の特に便利なことは、周りの物体で当たってもいい物が指定できます。このシナリオの場合には、テーブルとブロックが接触してもいいと指定しています。

`DoPlace`関数の中身は下記の通りになります。

```c++
  bool DoPlace() {
    std::vector<moveit_msgs::PlaceLocation> location;
    moveit_msgs::PlaceLocation p;
    p.place_pose.header.frame_id = arm_.getPlanningFrame();
    p.place_pose.pose.position.x = 0.2;
    p.place_pose.pose.position.y = 0;
    p.place_pose.pose.position.z = 0.1;
    p.place_pose.pose.orientation.y = 0.707106;
    p.place_pose.pose.orientation.w = 0.707106;

    p.pre_place_approach.direction.header.frame_id = arm_.getPlanningFrame();
    p.pre_place_approach.direction.vector.z = -1;
    p.pre_place_approach.min_distance = 0.05;
    p.pre_place_approach.desired_distance = 0.07;

    p.post_place_retreat.direction.header.frame_id = arm_.getPlanningFrame();
    p.post_place_retreat.direction.vector.z = 1;
    p.post_place_retreat.min_distance = 0.05;
    p.post_place_retreat.desired_distance = 0.07;

    p.post_place_posture.joint_names.resize(1, "crane_plus_moving_finger_joint");
    p.post_place_posture.points.resize(1);
    p.post_place_posture.points[0].positions.resize(1);
    p.post_place_posture.points[0].positions[0] = 0.1;

    location.push_back(p);
    arm_.setSupportSurfaceName("table");
    ROS_INFO("Beginning place");
    arm_.place("sponge", location);
    ROS_INFO("Place done");
    return true;
  }
```

`DoPick`と同様に、どこに物体をどうやって置くか指定します。

コンパイルして実行するとマニピュレータはよりスムーズな動きでピック・アンド・プレースを行います。

_注意：MoveIt!は基本的に6DOF以上を持つマニピュレータ向きです。CRANE+のような4DOFマニピュレータのリチャブル・スペース（マニピュレータが届ける姿勢）はかなり限られていて、プラニングが難しいです。MoveIt!はプラニングで失敗することが多くなります。_

_このソースは以下のURLでダウンロード可能です。_

<https://github.com/gbiggs/rsj_2017_pick_and_placer/tree/moveit_pick_place_plugin>