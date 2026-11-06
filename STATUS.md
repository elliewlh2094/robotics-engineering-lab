# 練習進度

只允許三種狀態：`未開始`／`進行中`／`實際做過`。

**誠實紀錄**：沒做完就不要標成「實際做過」。卡住、失敗、只跑過別人的範例，都寫在備註。來自教學或上游專案的程式不算自己的「實際做過」，除非你真的改過、測過並能說清楚。

最後更新：尚未開始（檔案建立於 2026-11-06，Asia/Taipei）

## 軟體開發（詳見 [docs/software-learning-path.md](docs/software-learning-path.md)）

| 階段 | 能做到 | 狀態 | 備註 |
| --- | --- | --- | --- |
| S1 | C++17 差速運動學類別，Ubuntu 24.04 可編譯，有乾淨 Git 歷史 | 未開始 | |
| S1 | Robotisim §5 觀念表各做一題（const／RAII／unique_ptr／virtual／chrono） | 未開始 | |
| S1 | Python 腳本把運動學輸出畫成圖 | 未開始 | |
| S2 | S1 函式庫有 GTest（角度 wrap、dt、NaN、timeout） | 未開始 | |
| S2 | Dockerfile，`docker build` 後可編譯並跑測試 | 未開始 | |
| S2 | push 後 GitHub Actions 全綠 | 未開始 | |
| S3 | AMR-1 URDF，RViz 看到 TF 樹正確 | 未開始 | |
| S3 | rclcpp teleop node，把 S1 函式庫包成 ROS adapter | 未開始 | |
| S3 | 故意製造 QoS 不相容與 stale input 並診斷 | 未開始 | |
| S4 | AMR-1 在 Gazebo（DiffDrive plugin）用鍵盤開得動，說得出參數對應 | 未開始 | |
| S4 | DiffBot demo（example_2）跑起來，RViz 看到移動 | 未開始 | |
| S4 | 讀懂 DiffBot hardware interface 的 `read()`／`write()` | 未開始 | |
| S5 | Gazebo：建圖 → 存圖 → 定位 → 自主導航 | 未開始 | |
| S5 | 同一套設定搬到實機 AMR-1 跑通 | 未開始 | |
| S5 | 跑完 Robotisim §12 情境並記錄失敗案例 | 未開始 | |
| S6 | 自己寫的 Nav2 planner 或 controller plugin，附 GTest | 未開始 | |
| S6 | profile：比較 Python 與 C++ 版執行時間 | 未開始 | |
| S6 | README：版本、啟動、frame 圖、驗證方式，標明模擬結果 | 未開始 | |
| S7 | 一個被 merge 的開源 PR | 未開始 | |
| S8 選 | SO-101 模擬 MoveIt pick-and-place；有手臂則實機同一軌跡 | 未開始 | |
| S8 選 | AMR-1 自寫 ros2_control hardware interface | 未開始 | |
| S8 選 | AMR-1 加相機，偵測 ArUco 或顏色標記 | 未開始 | |
| S8 選 | 把 AMR-1 或 SO-101 匯入 Isaac Sim 或 MuJoCo | 未開始 | |
| S8 選 | 讀懂並修改 linorobot2_hardware 韌體 | 未開始 | |

## 硬體（詳見 [docs/hardware-learning-path.md](docs/hardware-learning-path.md)）

| 階段 | 能做到 | 狀態 | 備註 |
| --- | --- | --- | --- |
| H0 選 | 瀏覽器裡讓虛擬 LED 閃爍、讀虛擬按鈕 | 未開始 | |
| H1 | 三用電表量電池電壓、降壓輸出，並檢查短路 | 未開始 | |
| H1 | 幫 IMU／馬達驅動模組焊上排針，焊點合格 | 未開始 | |
| H2 | 畫出 AMR-1 電源樹並標共地 | 未開始 | |
| H2 | 說得出鋰電池充電、存放、過放與膨脹處理 | 未開始 | |
| H3 | ESP32：GPIO、PWM、中斷、I2C、UART 各一個小程式 | 未開始 | |
| H3 | 用中斷讀出單顆馬達編碼器計數 | 未開始 | |
| H4 | 透過驅動板控制兩顆馬達正反轉與 PWM 調速 | 未開始 | |
| H4 | 讀編碼器轉速，寫出簡單輪速 PID | 未開始 | |
| H4 | IMU 透過 I2C 讀出加速度與角速度 | 未開始 | |
| H5 | 示波器量出馬達 PWM 頻率與 duty cycle | 未開始 | |
| H5 | 看到編碼器 A／B 相波形並判斷轉向 | 未開始 | |
| H6 選 | 畫出並印出 LiDAR 支架或電池座，尺寸一次對上 | 未開始 | |
| H7 | 接好兩輪、驅動、IMU、MCU、Pi、LiDAR，完成電源分配 | 未開始 | |
| H7 | 燒錄韌體、完成馬達方向與編碼器校正 | 未開始 | |
| H7 | Pi 上看得到 `/odom/unfiltered`、`/imu/data`、`/scan`，鍵盤遙控走直線不偏 | 未開始 | |
| H8 選 | SO-101 完成組裝與校正，leader 遙控 follower | 未開始 | |
| H9 選 | ESP32 與 Pi 在 CAN bus 互傳電池電壓與輪速，`candump` 看得到 | 未開始 | |
| H9 選 | Pi 用 PyModbus 讀寫 Modbus RTU 裝置 | 未開始 | |
| H9 選 | AMR-1 用 MQTT 發布電量與狀態，另一台訂閱得到 | 未開始 | |

## 機器人學知識（詳見 [docs/robotics-theory-learning-path.md](docs/robotics-theory-learning-path.md)）

| 階段 | 能做到 | 狀態 | 備註 |
| --- | --- | --- | --- |
| K1 | 寫出 `base_link` 到 `laser` 的齊次轉換矩陣 | 未開始 | |
| K1 | 解釋為什麼 EKF 要對運動模型取 Jacobian | 未開始 | |
| K2 | 推導差速正／逆運動學，說明非完整約束 | 未開始 | |
| K2 | 解釋輪式里程計為何隨距離累積誤差 | 未開始 | |
| K2 選 | 用 Modern Robotics Ch 4–6 推 SO-101 正運動學與數值逆運動學 | 未開始 | |
| K3 | 單輪馬達一階模型，解釋 PID 三增益影響 | 未開始 | |
| K3 | 說得出輪速 PID 在低速、電壓下降、PWM 飽和時的問題 | 未開始 | |
| K3 | 解釋 LQR 與 PID 取捨，以及 Nav2 路徑追蹤控制器想法 | 未開始 | |
| K4 | 說明 EKF 為何融合輪式里程計與 IMU，以及失效情境 | 未開始 | |
| K4 | 解釋 AMCL 在長走廊、對稱環境為何容易失敗 | 未開始 | |
| K4 | 解釋 SLAM Toolbox 的 graph-based SLAM 三段作用 | 未開始 | |
| K5 | 比較 A\*／Dijkstra 與 RRT，說明 2D 佔用格為何多用圖搜尋 | 未開始 | |
| K5 | 解釋全域規劃器與局部控制器分工，以及 costmap 膨脹設錯的後果 | 未開始 | |
| K6 選 | 說明 RL／IL 與傳統方法的取捨 | 未開始 | |
| K6 選 | 用 LeRobot 在 SO-101 錄示範並訓練模仿學習策略 | 未開始 | |
