# 機器人硬體的學習路線

> 由使用者的 Heptabase 筆記轉換而來（轉換日期：2026-11-06，Asia/Taipei）。文中價格與連結的查核日期以內文為準（多為 2026-11-02）。

相關參考：[軟體路線](software-learning-path.md)、[機器人學知識路線](robotics-theory-learning-path.md)、[進度](../STATUS.md)。

## 這份文件在做什麼

- **三個目標**：
  1. 能寫控制系統
  2. 能做出想要功能的機器人
  3. 理解演算法的原理、優點與限制

- **這份文件只寫「HOW TO WIRE」**：怎麼把機器人接起來、組起來、量得出問題（電路、電源與電池安全、MCU、馬達與感測器、示波器、CAD／3D 列印、整機組裝、工業通訊）。軟體架構與演算法數學不在這裡展開。

- **目標深度**：做到「能自己組裝、能自己除錯」就好，不走 PCB 設計或電機控制器開發。

- **查詢與連結驗證日期**：2026-11-02（Asia/Taipei）。價格也是這天查的，會變動。

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
  - 其他：帶編碼器減速馬達 ×2（例如 JGB37-520 或 37D 磁編碼器馬達）、LD19（台灣買得到的是 LDROBOT D500 開發套件）、3S 電池、降壓模組、底盤。
  - **一體板選項**：Waveshare General Driver for Robots，一塊板整合 ESP32、TB6612、QMI8658 IMU、AK09918 磁力計、INA219 電壓電流計，還有 LD19 接頭（來源：linorobot2_hardware wiki）。**只能做 2WD**。可以省掉分開的驅動、IMU 與大部分接線；代價是 H3／H4 自己接線的練習變少（推測）。

- **選修第二台：SO-101 手臂**（[SO-ARM100 repo](https://github.com/TheRobotStudio/SO-ARM100)、[LeRobot SO-101 文件](https://huggingface.co/docs/lerobot/so101)），只用在 H8。

- 每個階段的「能做到」都以 AMR-1 的某個子系統驗收，最後在 H7 組成整機。

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

必修合計約 140h（推測）。

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

## H2 電源與鋰電池安全（必修，~8h）

- [MIT EHS Lithium Battery Safety Guidance（PDF）](https://ehs.mit.edu/wp-content/uploads/2019/09/Lithium_Battery_Safety_Guidance.pdf)

- **能做到**：
  - [ ] 畫出 AMR-1 的電源樹：電池 → 保險絲／開關 → 馬達驅動；電池 → 降壓 5V → Pi／MCU，並標出共地
  - [ ] 說得出鋰電池充電、存放、過放與膨脹時的處理方式

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

- **購買與電源**：台灣賣的 PRO 套件 follower 是 **12V** STS3215（C018／C047），不是官方預設的 7.4V C001，要接 12V 電源；leader 仍是 7.4V，兩種電壓不能混接（來源：機器人王國商品頁、iCShop 搜尋摘要、Seeed wiki，2026-11-02 查）。自己有 3D 印表機就買不含列印件的版本（差 NT$2,000）。

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

## 台灣零件價格表（查詢日期：2026-11-02）

| 項目 | 價格（NT$） | 通路／來源 |
| --- | --- | --- |
| LDROBOT D500（= LD19 開發套件，wiki 推薦） | 3,819 | [iCShop](https://www.icshop.com.tw/categories/sbc-lidar) |
| RPLIDAR C1（也支援） | 2,879 | [iCShop](https://www.icshop.com.tw/categories/sbc-lidar)；[飆機器人](https://shop.playrobot.com/products/ssl0092) 3,150（查詢時缺貨） |
| 12V 37D 磁編碼器減速馬達 | 620／顆 | [iCShop](https://www.icshop.com.tw/categories/gear-motors) |
| JGB37-520 編碼器減速馬達 | 約 200–400／顆（未重新確認） | 露天、蝦皮（[飛比比價](https://feebee.com.tw/s/jgb37-520/)） |
| TB6612FNG 馬達驅動 | 210 | [iCShop](https://www.icshop.com.tw/categories/motor-drive) |
| MPU6050 GY-521 IMU | 75–120 | [機器人王國](https://robotkingdom.com.tw/product/mpu-6050-imu-6dof-gy-521/) 120；[聯騰](https://www.ltc.com.tw/products/mtardgy521) 75 |
| Waveshare General Driver for Robots（一體板，只能 2WD） | 1,209 | [iCShop](https://www.icshop.com.tw/products?query=General%20Driver%20for%20Robots) |
| Raspberry Pi Pico 2 | 219 | [iCShop](https://www.icshop.com.tw/products?query=Pico%202) |
| ESP32-S3-DevKitC-1 | 499–899 | [iCShop](https://www.icshop.com.tw/products/368030501811) |
| Raspberry Pi 4（4GB） | 3,900 | [iCShop](https://www.icshop.com.tw/categories/raspberry-pi-boards) |
| Raspberry Pi 5（可選） | 2,499–12,500（搜尋摘要顯示售完） | [iCShop](https://www.icshop.com.tw/categories/raspberry-pi-boards) |
| 手持示波器 FNIRSI DSO152／DSO-TC3 | 約 600–1,900 | [BigGo 比價](https://biggo.com.tw/s/fnirsi-dso%20152) |
| Arduino Starter Kit | 4,150 | [iCShop](https://www.icshop.com.tw/pages/arduino-project-best)；其他套件見[飆機器人](https://shop.playrobot.com/categories/kits-arduino) |
| SN65HVD230 CAN 收發器模組（H9） | 85–95 | [露天](https://www.ruten.com.tw/item/22310151497173/)；[廣華電子 CJMCU-230](https://shop.cpu.com.tw/product/51686/info/) 160 |
| MCP2515 + TJA1050 CAN 模組（H9） | 150 | [iCShop](https://www.icshop.com.tw/categories/wired) |
| MAX485 TTL 轉 RS-485 模組（H9） | 15 | [iCShop](https://www.icshop.com.tw/categories/wired) |
| Waveshare 樹莓派 RS485／CAN 擴充板（H9） | 389 | [iCShop](https://www.icshop.com.tw/categories/wired) |
| SO-101 PRO 套件，不含列印件（H8） | 9,500（含稅 9,975），有庫存 | [機器人王國](https://robotkingdom.com.tw/product/so-arm101-ai-arm-kit-pro/) |
| SO-101 PRO 套件，含列印件（H8） | 11,500（含稅 12,075），有庫存 | [機器人王國](https://robotkingdom.com.tw/product/so-arm101-ai-arm-kit-pro-3d/) |
| SO-101 已組裝校正（H8） | 13,500（含稅 14,175）；另有附 2 顆 PW315 相機的組裝版 17,200 | 機器人王國 |
| SO-101 PRO（Seeed，H8） | 9,300–11,000，標示售完／下單後訂貨 | [iCShop](https://www.icshop.com.tw/products/368040500233) |
| Feetech STS3215 伺服（備品，含 12V C018／C047） | 640／顆 | [機器人王國](https://robotkingdom.com.tw/product/feetech-st-3215-c018-c047-servo/) |

- **AMR-1 核心零件粗估**：Pi 4 4GB 3,900 + Pico 2 219 + TB6612 210 + 37D 馬達 620 ×2 + D500 3,819 + MPU6050 約 100 ≈ **NT$9,500**（推測，不含電池、充電器、降壓模組、底盤與線材）。改用 Waveshare 一體板取代 Pico 2 + TB6612 + IMU，核心約 NT$10,200（推測）。
- **AMR-1 估計總價**：約 NT$11,000–12,000（推測）。電池、充電器、降壓模組、底盤、線材約 NT$1,500–2,500（推測，未逐項查價）。
- **現成平台參考（不自組時）**：[TurtleBot3（艾迪麥）](https://idminer.com.tw/product/turtlebot3/) 需詢價；[ZanRobot ROS SharkBot](https://zanrobot.com/shop/ros_sharkbot/) 18,500；[ROSKY2（iCShop）](https://www.icshop.com.tw/products/368030200711) 35,000。

## 限制／注意

- **馬達驅動照韌體 wiki**：3S／12V 用 TB6612，韌體設 `USE_BTS7960_MOTOR_DRIVER`（2-PWM 模式）；避免 L298N（會發熱），優先選 MOSFET 驅動（來源：[linorobot2_hardware wiki](https://github.com/linorobot/linorobot2_hardware/wiki)，2026-11-02 查）。TB6612 連續電流約 1.2A（SparkFun Hookup Guide），換更大的馬達或更高電壓時再確認堵轉電流（推測）。
- **光達選 LD19**：wiki 不建議新手用 RPLIDAR A1／A2。台灣通路賣的是 LDROBOT D500（LD19 開發套件）；RPLIDAR C1 也支援，便宜 NT$940。
- **Waveshare General Driver for Robots 只能做 2WD**：之後想改 4WD 或麥克納姆輪就要換板。
- **SO-101 的 12V follower**：台灣 PRO 套件的 follower 是 12V C018／C047，和官方 7.4V C001 不同，要配 12V 電源；不要拿 7.4V 伺服接 12V。
- **ESP32-S3**：linorobot2_hardware jazzy README 已列出 `esp32s3` 環境（2026-11-02 查），但 IMU、驅動腳位要照 config 檔自己對應。
- **ESP32 的 TWAI 需要外接 CAN 收發器**（如 SN65HVD230）才能接上 CAN bus。
- **SO-101 價格 2026-11-02 已重新確認**：機器人王國有庫存；iCShop 的價格與售完狀態只來自搜尋摘要。
- **Raspberry Pi 5 價格區間沒有按 RAM 拆開**，售完狀態來自搜尋摘要；Linorobot2 用 Pi 4 4GB 就夠跑（Pi 4 是最低需求）。
- **還沒查到台灣價格**：JGB37-520（表中為舊的比價範圍）、Waveshare TP200／MC200 底盤（iCShop 是代理，需洽詢）、3S 電池與充電器。
- **零件價格會變動**，露天／蝦皮價格差異大；表中是 2026-11-02 看到的價格。
- **時數都是推測**。

## 來源

- 共同載具：[Linorobot2](https://github.com/linorobot/linorobot2)、[linorobot2_hardware](https://github.com/linorobot/linorobot2_hardware)、[linorobot2_hardware wiki](https://github.com/linorobot/linorobot2_hardware/wiki)、[Linorobot2 docs](https://linorobot.github.io/linorobot2/)
- 教材：SparkFun、Adafruit、Random Nerd Tutorials、DroneBot Workshop、toptechboy、Onshape、Teaching Tech、OrcaSlicer、CSS Electronics、Espressif ESP-IDF、Linux kernel docs、Modbus Organization、PyModbus、HiveMQ、Eclipse Mosquitto（連結見各階段）
- 價格：iCShop、飆機器人、露天、廣華電子、飛比、BigGo、機器人王國、聯騰、艾迪麥、ZanRobot（連結見價格表）
