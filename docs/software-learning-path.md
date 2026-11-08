# 機器人軟體開發的學習路線

> 由使用者的 Heptabase 筆記轉換而來（轉換日期：2026-11-06，Asia/Taipei）。文中連結的查核日期以內文為準（多為 2026-11-02）。

相關參考：[硬體路線](hardware-learning-path.md)、[機器人學知識路線](robotics-theory-learning-path.md)、[進度](../STATUS.md)。

## 這份文件在做什麼

- **三個目標**：
  1. 能寫控制系統
  2. 能做出想要功能的機器人
  3. 理解演算法的原理、優點與限制

- **這份文件只寫「HOW TO WRITE」**：怎麼把機器人軟體寫出來、測好、跑起來（Python／C++、Linux、Git、CMake、測試、Docker、CI、ROS 2、Nav2、`ros2_control`、模擬器、開源貢獻）。演算法背後的數學與硬體接線不在這裡展開。

- **學習重點**：主攻 AMR。多旋翼是選修載具，用來練飛行控制與狀態估測，不取代地面導航。

- **查詢與連結驗證日期**：既有連結 2026-11-02（Asia/Taipei）。多旋翼相關連結 2026-11-07 另查過，可以開啟。

- **時數全部是推測**，以「已會 Python、有 ROS 2 + Gazebo 經驗、C++ 幾乎從零」為起點估計。

## 共同載具：AMR-1

- **AMR-1**：依 [Linorobot2（jazzy branch）](https://github.com/linorobot/linorobot2) 自組的 2WD 差速 AMR。
  - 上位機：Raspberry Pi 4／5，Ubuntu 24.04 + ROS 2 Jazzy（Jazzy 需 C++17）。
  - 下位機：ESP32 或 Raspberry Pi Pico，跑 micro-ROS 韌體 [linorobot2_hardware](https://github.com/linorobot/linorobot2_hardware)：訂閱 `/cmd_vel`、跑輪速 PID，發布 `/odom/unfiltered` 與 `/imu/data`，再由 EKF 融合成 `/odom`。
  - 感測與致動：帶編碼器減速馬達 ×2、馬達驅動（TB6612）、IMU、LiDAR（韌體 wiki 建議新手用 LD19，RPLIDAR C1 也支援；來源：[linorobot2_hardware wiki](https://github.com/linorobot/linorobot2_hardware/wiki)，2026-11-02 查）。
  - Linorobot2 已整合 Nav2、SLAM Toolbox、`robot_localization`，**同一套設定可在 Gazebo 與實機上跑**（來源：[Linorobot2 docs](https://linorobot.github.io/linorobot2/)）。
  - **Linorobot2 沒有用 ros2_control**：2026-11-02 在 linorobot2 jazzy branch 全文搜尋找不到 `ros2_control`。Gazebo 模擬用的是 Gazebo 內建的 DiffDrive system plugin（`gz::sim::systems::DiffDrive`）；實機則是 MCU 上的 micro-ROS 韌體直接跑輪速 PID。所以 `ros2_control` 要另外用 DiffBot demo 或 SO-101 來學（見 S4）。

- **選修第二台：SO-101 手臂**，用在 S4 的 `ros2_control` 實機練習（選修）與 S8 的 MoveIt。

- **選修多旋翼**：不取代 AMR-1。多旋翼沒有 2D 導航堆疊（Nav2）。從 AMR-1 帶過去的，是 ROS 2 node 接到下層控制器這層架構，類似 micro-ROS 接到 `linorobot2_hardware`（推測）。
  - **第一台：Crazyflie 2.1+**（Bitcraze，1S 鋰聚合物電池）。硬體與韌體開源。[Getting started with the Crazyflie 2.x](https://www.bitcraze.io/documentation/tutorials/getting-started-with-crazyflie-2-x/) 的組裝涵蓋 2.1 與 2.1+。[Crazyflie 2.1 資料表 Rev 3](https://www.bitcraze.io/documentation/hardware/crazyflie_2_1/crazyflie_2_1-datasheet.pdf) 寫起飛重量 29 g、附 250 mAh 電池、組裝不用焊接（2026-11-07 查。2.1+ 沒有另找到一份規格表，重量先用這份 2.1 資料表）。C 韌體可以用 out-of-tree build 掛上自己的控制器與狀態估測器，不必改主韌體（來源：[Adding an estimator or controller](https://www.bitcraze.io/2023/02/adding-an-estimator-or-controller/)、[Out of tree build](https://www.bitcraze.io/documentation/repository/crazyflie-firmware/master/development/oot/)）。預設估測器是 complementary filter；Flow deck v2、Loco positioning deck、Lighthouse deck、動作捕捉擴充板會改用 extended Kalman filter（來源：[State estimation](https://www.bitcraze.io/documentation/repository/crazyflie-firmware/master/functional-areas/sensor-to-control/state_estimators/)）。外接定位系統的概覽見 [Positioning systems](https://www.bitcraze.io/documentation/system/positioning/)。[Crazyswarm2](https://github.com/IMRCLab/crazyswarm2) 提供 ROS 2 控制；安裝文件列了 Ubuntu 24.04 + ROS 2 Jazzy。模擬以 software-in-the-loop 跑 Crazyflie 韌體，ROS 介面和實機相同（來源：[installation](https://imrclab.github.io/crazyswarm2/installation.html)、[overview](https://imrclab.github.io/crazyswarm2/overview.html)，2026-11-07 查）。
  - **接著是 PX4 模擬的 Holybro X500**，Gazebo 模型 `gz_x500`。排在 S5 之後。ROS 2 經 uXRCE-DDS 送 offboard 位置設定點。模擬模型 `x500_base` 的機體質量是 2.0 kg（來源：[Gazebo Vehicles](https://docs.px4.io/main/en/sim_gazebo_gz/vehicles)、[x500_base/model.sdf](https://github.com/PX4/PX4-gazebo-models/blob/main/models/x500_base/model.sdf)，2026-11-07 查）。
  - **進階選修：實體 Holybro X500 V2**（Pixhawk 6C，4S）加 Raspberry Pi 伴隨電腦，走 uXRCE-DDS（TELEM2）。動力、電池與戶外飛行見硬體路線。
  - **ESP-FLY**（Seeed，XIAO ESP32-S3）只放硬體路線，當焊接練習。README 寫韌體改自 [ESP-Drone](https://github.com/espressif/esp-drone) 與 Circuit Digest 的修改版；ESP-Drone 的飛行控制核心來自 Crazyflie 韌體 2021.01。沒有 ROS 2、沒有模擬（來源：[Co-Create_ESP-FLY](https://github.com/Seeed-Projects/Co-Create_ESP-FLY)，2026-11-07 查）。
  - 考慮過 Duckiedrone DD24 與 Betaflight 競速機，沒有放進路線：這邊要能外掛自寫控制器與狀態估測，並且接上 ROS 2，這兩台沒有一起滿足（推測）。

- 地面階段的「能做到」以 AMR-1（或它的 Gazebo 版本）驗收。多旋翼階段以 Crazyflie 或 PX4 的 `gz_x500` 驗收。

## 路線總覽

| 階段 | 內容 | 必修／選修 | 時數（推測） |
| --- | --- | --- | --- |
| S1 | C++／Python／Linux／Git | 必修 | ~100h |
| S2 | CMake、GTest、Docker、CI | 必修 | ~40h |
| S3 | ROS 2 基礎（rclcpp／rclpy、URDF、測試） | 必修 | ~60h |
| S4 | Gazebo 模擬（AMR-1 的 DiffDrive plugin）+ `ros2_control`（DiffBot demo） | 必修 | ~50h |
| S5 | 定位與導航（SLAM Toolbox、`robot_localization`、Nav2、BT） | 必修 | ~80h |
| S9 | 多旋翼：Crazyswarm2 與 PX4 offboard | 選修 | ~40h |
| S6 | 自己寫演算法：C++ 重寫 PID／EKF、Nav2 plugin | 必修 | ~50h |
| S7 | 開源貢獻：一個被 merge 的 PR | 必修 | ~30h |
| S8 | 延伸：MoveIt 2 + SO-101 `ros2_control`、OpenCV、Isaac Sim／MuJoCo、嵌入式 C + micro-ROS、AMR-1 自寫 `ros2_control` hardware interface | 選修 | ~160h |

必修合計約 410h，選修約 200h（推測）。S8 約 160h，S9 約 40h。S9 的編號接在 S8 之後，不重排 S1–S8。建議 S5 做完就做 S9；S6 不依賴它。

## 這條路線的核心做法

- **先寫和 ROS 無關的純 C++ 函式庫，再包 ROS adapter**（來源：[Robotisim roadmap](https://robotisim.com/blog/software-engineer-to-robotics-engineer-roadmap) §5.3）。
- **profile before rewriting；Python 版與 C++ 版用 tolerance 比對**（來源：Robotisim §5）。
- **不必先學 C 再學 C++**：C 放到最後的選修嵌入式階段（推測）。
- **先跑通別人的 baseline（Linorobot2／Nav2 預設），再換成自己寫的元件**（來源：Robotisim §8）。多旋翼先跑 Crazyswarm2 的模擬與 PX4 的 offboard 範例。自寫估測器在知識路線 K4。

## S1 程式基礎（必修，~100h）

- **C++ 中文影片主線**：臺大 孔令傑「程式設計」（[臺大 OCW](https://ocw.aca.ntu.edu.tw/courses/111S107)、[YouTube 播放清單](https://www.youtube.com/playlist?list=PLMHSr8fseBzUvwjKtR6pX0Vv9Q9H-V3lY)）
  - 2022 年 9 月授課，15 講，有影片、PDF 講義與參考程式。前半是接近 C 的程序式寫法（指標、C Strings），後半是 Classes、Inheritance、Templates、Vectors、Exceptions。
  - 課程頁沒有寫明 C++11 以後的內容，modern 寫法用 learncpp 補（推測）。

- **Modern C++ 文字主線**：[learncpp.com](https://www.learncpp.com/)，補 smart pointers、move、lambda、`std::optional`。

- **Linux／shell／Git**：[MIT Missing Semester](https://missing.csail.mit.edu/)。

- **Python**：已具備，不另排教材（推測）。

- **能做到**：
  - [ ] 一個 C++17 的 AMR-1 差速運動學類別（輪速 ↔ 線速度／角速度），在 Ubuntu 24.04 編譯，有乾淨的 Git 歷史
  - [ ] 對照 Robotisim §5 的觀念表各做一題：`const` 傳 pose、RAII mock device、`unique_ptr` ownership、virtual interface、`std::chrono` 注入 clock
  - [ ] 一支 Python 腳本把運動學輸出畫成圖

## S2 工程習慣（必修，~40h）

- [Modern CMake](https://cliutils.gitlab.io/modern-cmake/)：建立 library、executable、test 三個 target。
- [GoogleTest CMake quickstart](https://google.github.io/googletest/quickstart-cmake.html)
- [Docker get-started](https://docs.docker.com/get-started/) + [ROS 2 Jazzy：在 Docker 裡跑 node](https://docs.ros.org/en/jazzy/How-To-Guides/Run-2-nodes-in-single-or-separate-docker-containers.html)
- [ros-tooling/action-ros-ci](https://github.com/ros-tooling/action-ros-ci)：GitHub Actions 上的 ROS 2 CI。

- **能做到**：
  - [ ] S1 的函式庫有 GTest（角度 wrap、dt 改變、NaN 輸入、timeout）
  - [ ] 有 Dockerfile，`docker build` 後可在乾淨環境編譯並跑測試
  - [ ] push 後 GitHub Actions 全綠

## S3 ROS 2 基礎（必修，~60h）

- [ROS 2 Jazzy Tutorials](https://docs.ros.org/en/jazzy/Tutorials.html)，重點：
  - [C++ publisher／subscriber](https://docs.ros.org/en/jazzy/Tutorials/Beginner-Client-Libraries/Writing-A-Simple-Cpp-Publisher-And-Subscriber.html)、[C++ action server／client](https://docs.ros.org/en/jazzy/Tutorials/Intermediate/Writing-an-Action-Server-Client/Cpp.html)：從 rclpy 轉到 rclcpp 的主線
  - [URDF](https://docs.ros.org/en/jazzy/Tutorials/Intermediate/URDF/URDF-Main.html)
  - [Testing](https://docs.ros.org/en/jazzy/Tutorials/Intermediate/Testing/Testing-Main.html)

- **練習規格**：Robotisim §7（QoS、TF、lifecycle）。

- **能做到**：
  - [ ] 寫出 AMR-1 的 URDF，在 RViz 看到 TF 樹正確
  - [ ] 用 rclcpp 寫 teleop node，把 S1 函式庫包成 ROS adapter
  - [ ] 故意製造 QoS 不相容與 stale input，用 `ros2 topic info --verbose`、`ros2 topic hz` 診斷

## S4 模擬 + ros2_control（必修，~50h）

- [Gazebo Harmonic get started](https://gazebosim.org/docs/harmonic/getstarted/)（Jazzy 對應 Gazebo Harmonic）
- [ros2_control Demos (Jazzy)](https://control.ros.org/jazzy/doc/ros2_control_demos/doc/index.html)，從 [example_2 DiffBot](https://control.ros.org/jazzy/doc/ros2_control_demos/example_2/doc/userdoc.html) 開始
- Articulated Robotics「Building a mobile robot」：[ros2_control concepts & simulation](https://articulatedrobotics.xyz/tutorials/mobile-robot/applications/ros2_control-concepts/)、[ros2_control on real hardware](https://articulatedrobotics.xyz/tutorials/mobile-robot/applications/ros2_control-real/)（[YouTube 播放清單](https://www.youtube.com/playlist?list=PLunhqkrRNRhYAffV8JDiFOatQXuU-NnxT)）。免費、從零做一台低成本差速車；模擬與控制篇已用 Jazzy，但專案總覽仍是 Foxy 時代的原型。

- **AMR-1 模擬照它實際的做法練**：Linorobot2 的 Gazebo 版用 Gazebo DiffDrive system plugin，不是 `ros2_control`。設 `LINOROBOT2_BASE=2wd` 後 `ros2 launch linorobot2_gazebo gazebo.launch.py`（來源：[Linorobot2 docs](https://linorobot.github.io/linorobot2/)），讀懂 URDF／SDF 裡 DiffDrive plugin 的輪距、輪徑、`/cmd_vel` 與 `/odom` 設定。

- **ros2_control 用 DiffBot demo 學**：上面 DiffBot 與 Articulated Robotics 教材就是主線，不套在 AMR-1 上。

- **SO-101 實機版（選修，有手臂才做）**：[adoodevv/so101_ros2](https://github.com/adoodevv/so101_ros2) 有 STS3215 的 `ros2_control` hardware plugin（沿用 LeRobot 校正、ROS 2 Jazzy），可以拿來對照 DiffBot 的 `read()`／`write()` 在真實序列馬達上怎麼寫。時數算在 S8 MoveIt。

- **練習規格**：Robotisim §11（controller 與 hardware interface 分開寫）。

- **能做到**：
  - [ ] AMR-1 在 Gazebo 裡（DiffDrive plugin）用鍵盤開得動，說得出 plugin 參數和實機韌體 config（輪距、輪徑）的對應
  - [ ] DiffBot demo（example_2，mock hardware）跑起來，透過 `diff_drive_controller` 送速度命令，在 RViz 看到它移動
  - [ ] 讀懂 DiffBot 的 hardware interface，說得出 `read()`／`write()` 在實機上要接什麼（例如對應到 AMR-1 的 micro-ROS `/cmd_vel` 與編碼器）

## S5 定位與導航（必修，~80h）

- [Nav2 Getting Started (Jazzy)](https://docs.nav2.org/jazzy/getting_started/)
- [Nav2 First-Time Robot Setup Guide](https://docs.nav2.org/jazzy/configuration_and_development/first_time_robot_setup_guide/)：TF、URDF、odometry、`robot_localization`、感測器、footprint、plugin 設定
- [slam_toolbox](https://github.com/SteveMacenski/slam_toolbox)
- [Nav2 Behavior Trees](https://docs.nav2.org/jazzy/getting_started/nav2_behavior_trees/) + [BehaviorTree.CPP](https://www.behaviortree.dev/)
- [Linorobot2](https://github.com/linorobot/linorobot2)：設定不確定時拿來對照的已知可用範本

- **練習規格**：Robotisim §12（Nav2 情境矩陣，baseline 先跑通再改）。

- **能做到**：
  - [ ] AMR-1 在 Gazebo 建圖 → 存圖 → 定位 → 給目標點自主導航
  - [ ] 同一套設定搬到實機 AMR-1 上跑通
  - [ ] 跑完 Robotisim §12 的情境（窄道、動態障礙、目標在障礙內等），記錄失敗案例

## S9 多旋翼：Crazyswarm2 與 PX4 offboard（選修，~40h）

建議 S5 做完就做。先看過 AMR-1 那條「ROS 2 node 到下位機」的路。這段沒有 Nav2：ROS 2 送到設定點為止，閉環在飛行控制器裡。串級與估測見知識路線 K3、K4。組裝與電池見硬體路線 H10。

先跑 Crazyswarm2 的模擬，再視手上有沒有實機 Crazyflie 2.1+。PX4 接在後面。

- [Crazyswarm2 usage](https://imrclab.github.io/crazyswarm2/usage.html)：`ros2 launch crazyflie launch.py backend:=sim`。模擬以 software-in-the-loop 跑 Crazyflie 韌體（[overview](https://imrclab.github.io/crazyswarm2/overview.html)）。
- 同一支 node 換到實機 Crazyflie 2.1+ 做自主飛行時，要有無線電傳輸器（Crazyradio 這類）和定位擴充板（例如 Flow deck）。沒有這兩樣，就停在模擬。
- [Gazebo Vehicles](https://docs.px4.io/main/en/sim_gazebo_gz/vehicles)：`make px4_sitl gz_x500`
- [uXRCE-DDS](https://docs.px4.io/main/en/middleware/uxrce_dds)：模擬的 client 走 UDP 8888，agent 用 `MicroXRCEAgent udp4 -p 8888`
- [ROS 2 Offboard Control](https://docs.px4.io/main/en/ros2/offboard_control)：rclcpp node 持續送 `OffboardControlMode` 與 `TrajectorySetpoint`。流量低於大約 2 Hz 會退出 offboard；官方範例是 100 ms 送一次
- **實體 X500 V2（選）**：[Raspberry Pi Companion](https://docs.px4.io/main/en/companion_computer/pixhawk_rpi) 把 Pi 接到 TELEM2，`UXRCE_DDS_CFG=102`，在 Pi 上跑 `sudo MicroXRCEAgent serial --dev /dev/serial0 -b 921600`，沿用模擬那支 node。實機要留能接手手動飛行的辦法

- **能做到**：
  - [ ] 一支 ROS 2 node 讓 Crazyflie 起飛、經過三個航點、降落。先用 `backend:=sim`。換實機時說得出還要無線電傳輸器與定位擴充板
  - [ ] 一支 rclcpp node 讓 `gz_x500` 以 offboard 飛一個正方形。`OffboardControlMode` 與 `TrajectorySetpoint` 的發送不低於 2 Hz。範例程式標出來源
  - [ ] （選）同一支 offboard node 在 X500 V2 的 Raspberry Pi 上跑起來

## S6 自己寫演算法元件（必修，~50h）

- **C++ 重寫 PID／EKF**：
  - 先用 Python 寫差速模型 + PID heading controller（Robotisim §3–4 的公式與 `wrap_to_pi`），再改寫成不依賴 ROS 的 C++ 純函式庫（介面參考 Robotisim 的 `std::optional<BodyCommand> compute_command(...)`）。
  - EKF 用 C++ + **Eigen** 實作，和 Python 版用 tolerance 比對。
  - 對照答案：[onlytailei/CppRobotics](https://github.com/onlytailei/CppRobotics)（EKF、PF、A\*、RRT、DWA、LQR、MPC；CMake + Eigen）。最後 push 是 2024-03-08，**當對照答案就好，不當最佳寫法範本**（推測）。

- **Nav2 plugin**：[Nav2 Plugin Tutorials (Jazzy)](https://docs.nav2.org/jazzy/tutorials/plugin_tutorials/)，從 [Writing a New Planner Plugin](https://docs.nav2.org/jazzy/tutorials/plugin_tutorials/writing_new_planner_plugin/writing_new_planner_plugin/) 開始。

- **能做到**：
  - [ ] 自己寫的 planner 或 controller plugin 取代 AMR-1 的 Nav2 預設元件，附 GTest
  - [ ] profile：比較 Python 與 C++ 版執行時間
  - [ ] README：版本、啟動步驟、frame 圖、驗證方式，並標明哪些是模擬結果（Robotisim §13）

## S7 開源貢獻（必修，~30h）

- [Nav2 Getting Involved](https://docs.nav2.org/jazzy/community/getting_involved/)
- [Nav2 good first issues](https://github.com/ros-navigation/navigation2/issues?q=is%3Aissue+is%3Aopen+label%3A%22good+first+issue%22)
- 也可以挑 Linorobot2 或 `slam_toolbox` 這類用過的專案（推測）。

- **能做到**：
  - [ ] 一個被 merge 的 PR（文件、測試或 bug fix 都算），連結留下備註

## S8 延伸（選修，~160h）

- **MoveIt 2 + SO-101 ros2_control（選修，~40h）**：[MoveIt 2 Getting Started](https://moveit.picknik.ai/main/doc/tutorials/getting_started/getting_started.html)；載具是 SO-101。MOGI-ROS 的課程 repo（[GitHub org](https://github.com/orgs/MOGI-ROS/repositories)）可當補充。
  - SO-101 的 ROS 2 套件：[ros-physical-ai/ros2_so_arm](https://github.com/ros-physical-ai/ros2_so_arm)（description、Gazebo、MoveIt config；`hardware_type:=mock_components / real / gazebo / mujoco`；CI 跑 Jazzy、Kilted、Rolling）或 [adoodevv/so101_ros2](https://github.com/adoodevv/so101_ros2)（STS3215 `ros2_control` hardware plugin、MoveIt 2 的 OMPL／Pilz／STOMP、Gazebo Harmonic；Jazzy）。2026-11-02 查。
  - 做法：先用 `demo.launch.py` 配 mock hardware（`mock_components`）跑 MoveIt，有實機再換成 STS3215 hardware plugin。
  - 能做到：SO-101 在模擬中用 MoveIt 規劃 pick-and-place；有手臂的話，用 so101_ros2 的 hardware plugin 讓實機跟著同一條軌跡跑，說得出它的 `read()`／`write()` 和 DiffBot 的差異。

- **AMR-1 自寫 ros2_control hardware interface（延伸挑戰，~30h，推測）**：Linorobot2 本身沒有這層，這是自己加的專案，沒有現成教材。推測的做法：寫一個 `SystemInterface`，`write()` 把 `diff_drive_controller` 的輪速命令送給 MCU，`read()` 讀回編碼器輪速；MCU 韌體可能要改成接受輪速、回傳輪速，而不是直接吃 `/cmd_vel`。參考 DiffBot example_2 與 Articulated Robotics 的實機篇。
  - 能做到（推測）：AMR-1 改用 `diff_drive_controller` 控制，用鍵盤開得動，`/odom` 與原韌體版本差不多。

- **OpenCV（選修，~20h）**：[OpenCV Bootcamp](https://courses.opencv.org/courses/course-v1:OpenCV+Bootcamp+CV0/about)。
  - 能做到：AMR-1 加相機，偵測 ArUco 或顏色標記並發布位置。

- **Isaac Sim 或 MuJoCo（選修，~30h）**：[Isaac Sim docs](https://docs.isaacsim.omniverse.nvidia.com/latest/index.html)（需要 RTX 等級 GPU）、[MuJoCo docs](https://mujoco.readthedocs.io/en/stable/overview.html)。
  - 能做到：把 AMR-1 或 SO-101 匯入其中一個模擬器並跑起來。

- **嵌入式 C + micro-ROS（選修，~40h）**：
  - 路線：[MissionRobo Embedded Robotics Engineer](https://www.missionrobo.com/roadmaps/embedded-robotics-engineer)（Modern C、STM32、interrupt／DMA、SPI／I2C／UART／CAN、FreeRTOS、micro-ROS）。MissionRobo 把 Embedded C 列為 Optional：「Enough to read a firmware engineer's code」。
  - [Modern C（Jens Gustedt，免費）](https://modern-c.gitlabpages.inria.fr/)：MissionRobo Embedded 稱為「The best free modern-C book」。
  - [micro-ROS](https://micro.ros.org/)
  - 能做到：讀懂並修改 AMR-1 的 `linorobot2_hardware` 韌體（例如調整 PID 參數或新增一個 topic）。

## 限制／注意

- **時數都是推測**：沒有來源提供這個組合的實際時程。
- **孔令傑課程是 2022 年的大一課**：頁面沒有標明 C++11 以後的內容，要靠 learncpp 補 modern 寫法（推測）。
- **CppRobotics 最後更新 2024-03-08**：只當對照答案。
- **Articulated Robotics 的專案總覽仍是 Foxy 時代**：模擬與 `ros2_control` 篇已更新到 Jazzy，其他篇照做時注意版本差異。
- **Isaac Sim 需要 RTX 等級 GPU**：沒有合適顯卡就改用 MuJoCo 或只用 Gazebo。
- **AMR-1 的硬體照韌體 wiki 建議**：3S／12V 用 TB6612（韌體設 `USE_BTS7960_MOTOR_DRIVER` 的 2-PWM 模式）、光達用 LD19（來源：[linorobot2_hardware wiki](https://github.com/linorobot/linorobot2_hardware/wiki)，2026-11-02 查）。電池、底盤還沒定。
- **Linorobot2 沒有 ros2_control**：S4 的 `ros2_control` 靠 DiffBot demo；若要在 AMR-1 上練習 `ros2_control` 底盤控制，得做 S8 的自寫 hardware interface（推測的做法，沒有現成教材）。
- **adoodevv/so101_ros2 很新**：2026-11-02 查時 14 ★，只支援 Jazzy；實機上可能遇到未知問題（推測）。
- **多旋翼不用 Nav2**：S9 練的是 ROS 2 到飛行控制器，不是把 S5 的 2D 導航作業搬過去。
- **PX4 官方 offboard 範例的版本**：該頁寫在 Ubuntu 20.04、ROS 2 Foxy、PX4 v1.14 上測過，編譯片段也有 Humble（2026-11-07 查）。本路線其他階段是 Jazzy，`px4_msgs` 的分支要自己對。
- **Crazyswarm2 的 Jazzy**：安裝文件把 Ubuntu 24.04 + ROS 2 Jazzy 列進對照表（2026-11-07 查）。模擬要另建 Crazyflie 韌體的 Python bindings，文件寫的測試版本是 2025.02。
- **ESP-FLY 不在這份文件練**：沒有 ROS 2、沒有模擬。焊接見硬體路線 H10。
- **Crazyflie 2.1 的 29 g 是 2.1 資料表**：2.1+ 若有不同的規格表，以那份為準。

## 來源

- 路線骨架：[Robotisim – Software Engineer to Robotics Engineer](https://robotisim.com/blog/software-engineer-to-robotics-engineer-roadmap)、[MissionRobo Embedded Robotics Engineer](https://www.missionrobo.com/roadmaps/embedded-robotics-engineer)
- 共同載具：[Linorobot2](https://github.com/linorobot/linorobot2)、[linorobot2_hardware](https://github.com/linorobot/linorobot2_hardware)、[linorobot2_hardware wiki](https://github.com/linorobot/linorobot2_hardware/wiki)、[Linorobot2 docs](https://linorobot.github.io/linorobot2/)
- SO-101 ROS 2：[ros-physical-ai/ros2_so_arm](https://github.com/ros-physical-ai/ros2_so_arm)、[adoodevv/so101_ros2](https://github.com/adoodevv/so101_ros2)
- 官方文件：[ROS 2 Jazzy](https://docs.ros.org/en/jazzy/Tutorials.html)、[Nav2](https://docs.nav2.org/jazzy/getting_started/)、[ros2_control](https://control.ros.org/jazzy/doc/ros2_control_demos/doc/index.html)、[Gazebo](https://gazebosim.org/docs/harmonic/getstarted/)
- 多旋翼：[Crazyswarm2](https://github.com/IMRCLab/crazyswarm2)、[Crazyflie out-of-tree](https://www.bitcraze.io/documentation/repository/crazyflie-firmware/master/development/oot/)、[Crazyflie state estimation](https://www.bitcraze.io/documentation/repository/crazyflie-firmware/master/functional-areas/sensor-to-control/state_estimators/)、[PX4 Gazebo vehicles](https://docs.px4.io/main/en/sim_gazebo_gz/vehicles)、[PX4 uXRCE-DDS](https://docs.px4.io/main/en/middleware/uxrce_dds)、[PX4 ROS 2 offboard](https://docs.px4.io/main/en/ros2/offboard_control)、[PX4 controller diagrams](https://docs.px4.io/main/en/flight_stack/controller_diagrams)、[PX4 EKF2](https://docs.px4.io/main/en/advanced_config/tuning_the_ecl_ekf)、[Holybro X500 V2](https://docs.px4.io/main/en/frames_multicopter/holybro_x500v2_pixhawk6c)、[Raspberry Pi companion](https://docs.px4.io/main/en/companion_computer/pixhawk_rpi)
