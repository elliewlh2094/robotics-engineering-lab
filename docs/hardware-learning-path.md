# 機器人硬體的學習路線

> 由使用者的 Heptabase 筆記轉換而來（轉換日期：2026-11-06，Asia/Taipei）。文中連結的查核日期以內文為準（多為 2026-11-02）。

相關參考：[軟體路線](software-learning-path.md)、[機器人學知識路線](robotics-theory-learning-path.md)、[進度](../STATUS.md)。

## 這份文件在做什麼

- **三個目標**：
  1. 能寫控制系統
  2. 能做出想要功能的機器人
  3. 理解演算法的原理、優點與限制

- **這份文件只寫「HOW TO WIRE」**：怎麼把機器人接起來、組起來、量得出問題（電路、電源與電池安全、MCU、馬達與感測器、示波器、CAD／3D 列印、整機組裝、工業通訊）。軟體架構與演算法數學不在這裡展開。

- **目標深度**：做到「能自己組裝、能自己除錯」就好，不走 PCB 設計或電機控制器開發。

- **查詢與連結驗證日期**：既有連結 2026-11-02（Asia/Taipei）。多旋翼相關連結 2026-11-07 另查過，可以開啟。

- **時數全部是推測**，以「沒碰過電子／MCU」為起點估計。

## 共同載具：AMR-1

- **AMR-1**：依 [Linorobot2（jazzy branch）](https://github.com/linorobot/linorobot2) 自組的 2WD 差速 AMR。
  - 上位機：Raspberry Pi 4／5（Linorobot2 要求至少 Pi 4），Ubuntu 24.04 + ROS 2 Jazzy。
  - 下位機：ESP32 或 Raspberry Pi Pico，跑 micro-ROS 韌體 [linorobot2_hardware](https://github.com/linorobot/linorobot2_hardware)（PlatformIO 編譯）：訂閱 `/cmd_vel`、跑輪速 PID，發布 `/odom/unfiltered` 與 `/imu/data`。
  - 韌體支援的 MCU：ESP32／ESP32-S2／ESP32-S3、Pico／Pico 2（W）；馬達驅動：通用 2-IN（如 L298）、通用 1-IN（如 MC33926）、BTS7960、ESC；IMU：BNO085、GY-85、MPU6050、MPU9150、MPU9250、QMI8658（來源：linorobot2_hardware jazzy README，2026-11-02 查）。
  - **韌體 wiki 的新手建議**（來源：[linorobot2_hardware wiki](https://github.com/linorobot/linorobot2_hardware/wiki)，2026-11-02 查）：
    - 馬達驅動：3S／12V 用 **TB6612**，韌體設 `USE_BTS7960_MOTOR_DRIVER`（2-PWM 模式）。避免 L298N（會發熱），優先選 MOSFET 驅動。
    - 光達：**LD19**。不建議新手用 RPLIDAR A1／A2。RPLIDAR C1 也在支援清單。
    - 馬達：12V、150–200 RPM、帶霍爾編碼器的有刷減速馬達。
  - 其他：帶編碼器減速馬達 ×2（例如 JGB37-520 或 37D 磁編碼器馬達）、LD19（LDROBOT D500 開發套件）、3S 電池、降壓模組、底盤。
  - **一體板選項**：Waveshare General Driver for Robots，一塊板整合 ESP32、TB6612、QMI8658 IMU、AK09918 磁力計、INA219 電壓電流計，還有 LD19 接頭（來源：linorobot2_hardware wiki）。**只能做 2WD**。可以省掉分開的驅動、IMU 與大部分接線；代價是 H3／H4 自己接線的練習變少（推測）。

- **選修第二台：SO-101 手臂**（[SO-ARM100 repo](https://github.com/TheRobotStudio/SO-ARM100)、[LeRobot SO-101 文件](https://huggingface.co/docs/lerobot/so101)），只用在 H8。

- **選修多旋翼**：不取代 AMR-1。軟體與演算法見另外兩條路線，這裡只寫組裝、焊接、電池與動力。
  - **焊接練習：ESP-FLY**（Seeed，XIAO ESP32-S3，1S）。README 的重量是約 18 g／25 g／28 g（不含鋰聚合物電池／含電池／再加 FPV 鏡頭）。要焊 XIAO 與馬達線。韌體是改過的 ESP-Drone，沒有 ROS 2、沒有模擬，所以停在硬體練習（來源：[Co-Create_ESP-FLY](https://github.com/Seeed-Projects/Co-Create_ESP-FLY)，2026-11-07 查）。第一台飛行載具是下面的 Crazyflie 2.1+。
  - **第一台：Crazyflie 2.1+**（Bitcraze，1S）。[Getting started with the Crazyflie 2.x](https://www.bitcraze.io/documentation/tutorials/getting-started-with-crazyflie-2-x/) 涵蓋 2.1 與 2.1+，步驟是裝電池與螺旋槳、擴充板插上。[Crazyflie 2.1 資料表 Rev 3](https://www.bitcraze.io/documentation/hardware/crazyflie_2_1/crazyflie_2_1-datasheet.pdf) 寫起飛重量 29 g、附 250 mAh 電池、組裝不用焊接（2026-11-07 查。2.1+ 沒有另找到規格表，重量先用這份）。
  - **進階：Holybro X500 V2**（Pixhawk 6C）。組裝頁寫 4S、16.8 V、5200 mAh 電池，馬達預裝在機臂上，經電子調速器與電源分配板供電，GPS 接 GPS1，遙測接 TELEM1（來源：[Holybro X500 V2 + Pixhawk 6C](https://docs.px4.io/main/en/frames_multicopter/holybro_x500v2_pixhawk6c)）。這是無刷動力，和 AMR-1 的有刷馬達加 TB6612 不同。模擬模型的機體質量是 2.0 kg；組裝頁沒寫實體整機重量，大約落在 1.3 到 2 公斤（推測。來源：[x500_base/model.sdf](https://github.com/PX4/PX4-gazebo-models/blob/main/models/x500_base/model.sdf)）。組裝頁也沒寫螺旋槳吋別，學習上先把它當成約 10 吋槳的戶外機（推測）。
  - 電池從 1S 練到 4S，做法寫在 H2。1S 是單顆電芯；4S 才用平衡充電器。

- 地面階段的「能做到」以 AMR-1 的子系統驗收，整機在 H7。多旋翼在 H10–H12，H1 與 H2 做完就能做，不用等 H7。

## 路線總覽

| 階段 | 內容 | 必修／選修 | 時數（推測） |
| --- | --- | --- | --- |
| H0 | 免費模擬器暖身（不用買零件） | 選修 | ~5h |
| H1 | 基本電路與工具（三用電表、焊接） | 必修 | ~25h |
| H2 | 電源與鋰電池安全 | 必修 | ~8h |
| H3 | MCU：Arduino → ESP32 | 必修 | ~30h |
| H4 | 馬達、驅動與感測器 | 必修 | ~30h |
| H5 | 示波器除錯 | 必修 | ~6h |
| H6 | CAD + 3D 列印 | 選修 | ~30h |
| H7 | AMR-1 整機組裝（linorobot2_hardware） | 必修 | ~40h |
| H8 | SO-101 手臂組裝 | 選修 | ~20h |
| H9 | 工業通訊：CAN、Modbus、MQTT | 選修 | ~20h |
| H10 | ESP-FLY 焊接練習 | 選修 | ~15h |
| H11 | Crazyflie 2.1+ 組裝 | 選修 | ~15h |
| H12 | Holybro X500 V2 動力與感測（進階） | 選修 | ~25h |

必修合計約 140h，多旋翼選修另約 55h（推測）。H10、H11 在 H1、H2 之後就能做，不用等 H7。H12 要先會 H2 的 4S 做法。

## H0 模擬器暖身（選修，~5h）

- [Learn Arduino（互動教學）](https://learn-arduino-red.vercel.app/es)、[Robotics Engineer（互動教學）](https://robotics-engineer.vercel.app)：免費的瀏覽器練習，買零件前先熟悉接線與程式。

- **能做到**：
  - [ ] 不買任何零件，在瀏覽器裡讓虛擬 LED 閃爍、讀一顆虛擬按鈕

## H1 基本電路與工具（必修，~25h）

- [Falstad Circuit Simulator](https://www.falstad.com/circuit/)：看電流怎麼流，驗證分壓、上拉電阻
- [Tinkercad Circuits](https://www.tinkercad.com/circuits)：在瀏覽器裡接麵包板
- [SparkFun：How to Use a Multimeter](https://learn.sparkfun.com/tutorials/how-to-use-a-multimeter)
- [Adafruit Guide to Excellent Soldering](https://learn.adafruit.com/adafruit-guide-excellent-soldering)

- **能做到**：
  - [ ] 用三用電表量出 AMR-1 電池電壓、降壓模組輸出，並檢查短路
  - [ ] 幫 IMU／馬達驅動模組焊上排針，焊點合格

整機的焊接練習是 H10 的 ESP-FLY。這裡先把排針焊好。

## H2 電源與鋰電池安全（必修，~8h）

- [MIT EHS Lithium Battery Safety Guidance（PDF）](https://ehs.mit.edu/wp-content/uploads/2019/09/Lithium_Battery_Safety_Guidance.pdf)

AMR-1 是 3S。多旋翼把範圍拉開：Crazyflie 2.1+ 與 ESP-FLY 是 1S（單顆電芯，沒有多顆電芯要平衡），Holybro X500 V2 是 4S（組裝頁寫 16.8 V）。4S 用平衡充電器。1S 的充電方式（Crazyflie 入門文件是接上電池後用 micro-USB 充）不要拿去充 4S。

- **能做到**：
  - [ ] 畫出 AMR-1 的電源樹：電池 → 保險絲／開關 → 馬達驅動；電池 → 降壓 5V → Pi／MCU，並標出共地
  - [ ] 說得出鋰電池充電、存放、過放與膨脹時的處理方式
  - [ ] 說得出 1S、3S、4S 的差別：誰要平衡充、久放前先放到存放電壓（不要滿電或過放久放）、充電時放在防火袋裡而且人不要離開、鼓包的電芯退出（不要再充、不要再裝上飛）

## H3 MCU：Arduino → ESP32（必修，~30h）

- [Paul McWhorter Arduino Lessons（toptechboy）](https://toptechboy.com/arduino-lessons/)：從零開始的影片 + 文章
- [Random Nerd Tutorials：Getting Started with ESP32](https://randomnerdtutorials.com/getting-started-with-esp32/)
- [Wokwi](https://wokwi.com)：線上模擬 Arduino／ESP32，沒有板子也能先練

- **能做到**：
  - [ ] 在 ESP32 上用 GPIO、PWM、中斷（interrupt）、I2C、UART 各寫一個小程式
  - [ ] 用中斷讀出 AMR-1 單顆馬達的編碼器計數

## H4 馬達、驅動與感測器（必修，~30h）

- [SparkFun TB6612FNG Hookup Guide](https://learn.sparkfun.com/tutorials/tb6612fng-hookup-guide/all)
- [DroneBot Workshop：DC Motors with L298N](https://dronebotworkshop.com/dc-motors-l298n-h-bridge/)（只用來理解 H 橋原理；L298N 雖然韌體支援，但 linorobot2_hardware wiki 建議避免，因為會發熱，AMR-1 用 TB6612）
- [Adafruit BNO085 IMU guide](https://learn.adafruit.com/adafruit-9-dof-orientation-imu-fusion-breakout-bno085/overview)：BNO085 也是 linorobot2_hardware 支援的 IMU，可直接用在 AMR-1

- **能做到**：
  - [ ] 透過驅動板控制 AMR-1 兩顆馬達正反轉與 PWM 調速
  - [ ] 讀出編碼器轉速，寫出簡單的輪速 PID
  - [ ] IMU 透過 I2C 讀出加速度與角速度

## H5 示波器除錯（必修，~6h）

- [SparkFun：How to Use an Oscilloscope](https://learn.sparkfun.com/tutorials/how-to-use-an-oscilloscope)

- **能做到**：
  - [ ] 用示波器量出 AMR-1 馬達 PWM 的頻率與 duty cycle
  - [ ] 看到編碼器的 A／B 相波形，判斷轉向

## H6 CAD + 3D 列印（選修，~30h）

- [Onshape Fundamentals: CAD](https://learn.onshape.com/learning-paths/onshape-fundamentals-cad)（免費帳號）
- [Teaching Tech 3D Printer Calibration](https://teachingtechyt.github.io/calibration.html)
- [OrcaSlicer Calibration wiki](https://github.com/OrcaSlicer/OrcaSlicer/wiki/Calibration)

- **能做到**：
  - [ ] 畫出並印出 AMR-1 的 LiDAR 支架或電池座，尺寸一次對上

## H7 AMR-1 整機組裝（必修，~40h）

- [linorobot2_hardware](https://github.com/linorobot/linorobot2_hardware)：韌體設定、接線、PlatformIO 燒錄、馬達與編碼器校正
- [Linorobot2 docs](https://linorobot.github.io/linorobot2/) + [Linorobot2 repo](https://github.com/linorobot/linorobot2)

- **能做到**：
  - [ ] 依韌體設定檔接好兩輪、驅動、IMU、MCU、Pi、LiDAR，並完成電源分配
  - [ ] 燒錄韌體、跑完馬達方向與編碼器校正
  - [ ] Pi 上看得到 `/odom/unfiltered`、`/imu/data`、`/scan`，用鍵盤遙控 AMR-1 走直線不偏

## H8 SO-101 手臂組裝（選修，~20h）

- [SO-ARM100 repo](https://github.com/TheRobotStudio/SO-ARM100)（含 SO-101）
- [LeRobot SO-101 文件](https://huggingface.co/docs/lerobot/so101)：組裝、馬達 ID 設定、校正

- **電源**：PRO 套件 follower 是 **12V** STS3215（C018／C047），不是官方預設的 7.4V C001，要接 12V 電源；leader 仍是 7.4V，兩種電壓不能混接（來源：Seeed wiki，2026-11-02 查）。

- **能做到**：
  - [ ] 完成組裝與校正，用 leader arm 遙控 follower arm

## H9 工業通訊：CAN、Modbus、MQTT（選修，~20h）

- EtherCAT 需要專用主站與從站硬體，不排入（推測）。

- **CAN**：
  - [CSS Electronics：CAN Bus Explained – A Simple Intro](https://www.csselectronics.com/pages/can-bus-simple-intro-tutorial)：frame、ID、仲裁、終端電阻
  - [ESP-IDF TWAI（ESP32 的 CAN 控制器）](https://docs.espressif.com/projects/esp-idf/en/stable/esp32/api-reference/peripherals/twai.html)（[ESP32-S3 版](https://docs.espressif.com/projects/esp-idf/en/stable/esp32s3/api-reference/peripherals/twai.html)）
  - [Linux SocketCAN 文件](https://docs.kernel.org/networking/can.html)：Pi 端用 `candump`／`cansend`
  - 延伸：[ros2_canopen](https://github.com/ros-industrial/ros2_canopen)（ROS 2 的 CANopen 驅動）

- **Modbus（RS-485）**：
  - [Modbus Organization 規格頁](https://www.modbus.org/modbus-specifications)：官方 Modbus Application Protocol 與 RTU over serial 規格
  - [PyModbus 文件](https://pymodbus.readthedocs.io/en/latest/)：在 Pi 上用 Python 當 Modbus RTU／TCP client

- **MQTT**：
  - [HiveMQ MQTT Essentials](https://www.hivemq.com/blog/mqtt-essentials-part-1-introducing-mqtt/)：publish／subscribe、QoS、retained message
  - [Eclipse Mosquitto](https://mosquitto.org/)：在 Pi 上架 broker
  - [ESP-IDF MQTT client](https://docs.espressif.com/projects/esp-idf/en/stable/esp32/api-reference/protocols/mqtt.html)

- **能做到**：
  - [ ] ESP32（TWAI + SN65HVD230 收發器）和 Pi（RS485／CAN HAT）在 CAN bus 上互傳 AMR-1 的電池電壓與輪速，用 `candump` 看到 frame
  - [ ] Pi 透過 MAX485 模組用 PyModbus 讀寫一台 Modbus RTU 裝置（或另一顆 ESP32 模擬的 slave）的 register
  - [ ] AMR-1 把電量與狀態用 MQTT 發布到 Mosquitto broker，另一台電腦訂閱得到

## H10 ESP-FLY 焊接（選修，~15h）

H1 的排針焊完、H2 的 1S 做法知道了再做。這是焊接練習，不是第一台飛行載具。第一台是 H11 的 Crazyflie 2.1+。

- [Co-Create_ESP-FLY](https://github.com/Seeed-Projects/Co-Create_ESP-FLY)：Seeed 的套件，主控是 XIAO ESP32-S3。README 寫含 1S 鋰聚合物電池約 25 g，要焊 XIAO 到驅動板、以及馬達線（2026-11-07 查）
- [esp-drone](https://github.com/espressif/esp-drone)：ESP-FLY README 寫這份韌體改自 Espressif 的 ESP-Drone，以及 Circuit Digest 的修改版。ESP-Drone 的飛行控制核心來自 Crazyflie 韌體 2021.01

沒有 ROS 2，也沒有模擬。軟體路線不把這台當載具。repo 裡另有一套偏 Betaflight 的韌體流程，這條路線不走那套。

- **能做到**：
  - [ ] 焊完可以上電。1S 的充電、存放與鼓包處理照 H2
  - [ ] 說得出它為什麼停在硬體練習：韌體沒有 ROS 2、沒有模擬

## H11 Crazyflie 2.1+ 組裝（選修，~15h）

不用焊接，所以不必等 H10。H2 的 1S 做法要先會。擴充板是插上的，不是焊上的。

- [Getting started with the Crazyflie 2.x](https://www.bitcraze.io/documentation/tutorials/getting-started-with-crazyflie-2-x/)
- [Crazyflie 2.1 datasheet Rev 3](https://www.bitcraze.io/documentation/hardware/crazyflie_2_1/crazyflie_2_1-datasheet.pdf)：29 g、250 mAh、不用焊接
- 估測器哪一種擴充板會切換，見知識路線 K4 與 [State estimation](https://www.bitcraze.io/documentation/repository/crazyflie-firmware/master/functional-areas/sensor-to-control/state_estimators/)

- **能做到**：
  - [ ] 依入門文件組好，電池裝得住、充得了，人在場
  - [ ] 指出 Flow deck、Loco positioning deck、Lighthouse deck 是插上去的定位擴充板。沒插的時候，預設估測器是 complementary filter

## H12 Holybro X500 V2 動力與感測（選修進階，~25h）

有實體機再做。先會 H2 的 4S：平衡充電器、防火袋、存放電壓、充電時人不要離開、鼓包的電芯退出。Pixhawk 6C，無刷馬達，電子調速器，電源分配板。

- [Holybro X500 V2 + Pixhawk 6C](https://docs.px4.io/main/en/frames_multicopter/holybro_x500v2_pixhawk6c)：4S 16.8 V 5200 mAh、電源模組 PM02、電源分配板、電子調速器接 PWM、GPS 接 GPS1、遙測接 TELEM1。馬達出廠已裝在機臂上，組裝是鎖螺絲與接插頭
- [Raspberry Pi Companion with Pixhawk](https://docs.px4.io/main/en/companion_computer/pixhawk_rpi)：伴隨電腦的 uXRCE-DDS 走 TELEM2。不要和 TELEM1 的遙測插到同一個座。ROS 2 的 offboard 見軟體路線 S11
- [Optical Flow](https://docs.px4.io/main/en/sensor/optical_flow)：之後可加 Holybro H-Flow 或 ARK Flow，給沒有 GPS 時的速度

戶外飛行前先遵守當地的無人機規範。

- **能做到**：
  - [ ] 畫出電源樹：電池 → 電源模組／電源分配板 → 電子調速器 → 無刷馬達，飛行控制器走電源模組，並標出共地
  - [ ] GPS 與遙測接到組裝頁寫的座。4S 的充電與存放照 H2，和 1S 的充法分開
  - [ ] （更後面）接上 H-Flow 或 ARK Flow，說得出它補的是沒有衛星定位時的速度

## 限制／注意

- **馬達驅動照韌體 wiki**：3S／12V 用 TB6612，韌體設 `USE_BTS7960_MOTOR_DRIVER`（2-PWM 模式）；避免 L298N（會發熱），優先選 MOSFET 驅動（來源：[linorobot2_hardware wiki](https://github.com/linorobot/linorobot2_hardware/wiki)，2026-11-02 查）。TB6612 連續電流約 1.2A（SparkFun Hookup Guide），換更大的馬達或更高電壓時再確認堵轉電流（推測）。
- **光達選 LD19**：wiki 不建議新手用 RPLIDAR A1／A2。LDROBOT D500 是 LD19 開發套件；RPLIDAR C1 也支援。
- **Waveshare General Driver for Robots 只能做 2WD**：之後想改 4WD 或麥克納姆輪就要換板。
- **SO-101 的 12V follower**：PRO 套件的 follower 是 12V C018／C047，和官方 7.4V C001 不同，要配 12V 電源；不要拿 7.4V 伺服接 12V。
- **ESP32-S3**：linorobot2_hardware jazzy README 已列出 `esp32s3` 環境（2026-11-02 查），但 IMU、驅動腳位要照 config 檔自己對應。
- **ESP32 的 TWAI 需要外接 CAN 收發器**（如 SN65HVD230）才能接上 CAN bus。
- **Raspberry Pi**：Linorobot2 用 Pi 4 4GB 就夠跑（Pi 4 是最低需求）。
- **1S 與 4S 不要混充**：Crazyflie 的 micro-USB 充電是 1S。X500 V2 的 4S 用平衡充電器。接頭與充電器分開。
- **焊接只強制在 ESP-FLY**：Crazyflie 2.1 資料表寫不用焊接。X500 V2 組裝頁是鎖螺絲與接插頭，馬達已裝在機臂上。
- **Crazyflie 的 29 g 來自 2.1 資料表**：路線上的第一台是 2.1+。2.1+ 若有自己的規格表，以那份為準。
- **X500 的 2.0 kg 是模擬模型的機體質量**：組裝頁沒寫實體整機重量，也沒寫螺旋槳吋別。
- **時數都是推測**。

## 來源

- 共同載具：[Linorobot2](https://github.com/linorobot/linorobot2)、[linorobot2_hardware](https://github.com/linorobot/linorobot2_hardware)、[linorobot2_hardware wiki](https://github.com/linorobot/linorobot2_hardware/wiki)、[Linorobot2 docs](https://linorobot.github.io/linorobot2/)
- 教材：SparkFun、Adafruit、Random Nerd Tutorials、DroneBot Workshop、toptechboy、Onshape、Teaching Tech、OrcaSlicer、CSS Electronics、Espressif ESP-IDF、Linux kernel docs、Modbus Organization、PyModbus、HiveMQ、Eclipse Mosquitto（連結見各階段）
- 多旋翼：[ESP-FLY](https://github.com/Seeed-Projects/Co-Create_ESP-FLY)、[ESP-Drone](https://github.com/espressif/esp-drone)、[Crazyflie 2.x getting started](https://www.bitcraze.io/documentation/tutorials/getting-started-with-crazyflie-2-x/)、[Crazyflie 2.1 datasheet](https://www.bitcraze.io/documentation/hardware/crazyflie_2_1/crazyflie_2_1-datasheet.pdf)、[Holybro X500 V2](https://docs.px4.io/main/en/frames_multicopter/holybro_x500v2_pixhawk6c)、[Raspberry Pi companion](https://docs.px4.io/main/en/companion_computer/pixhawk_rpi)、[PX4 optical flow](https://docs.px4.io/main/en/sensor/optical_flow)
