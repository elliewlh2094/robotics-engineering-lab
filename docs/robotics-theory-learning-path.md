# 機器人學知識的學習路線

> 由使用者的 Heptabase 筆記轉換而來（轉換日期：2026-11-06，Asia/Taipei）。文中連結的查核日期以內文為準（多為 2026-11-02）。

相關參考：[軟體路線](software-learning-path.md)、[硬體路線](hardware-learning-path.md)、[進度](../STATUS.md)。

## 這份文件在做什麼

- **三個目標**：
  1. 能寫控制系統
  2. 能做出想要功能的機器人
  3. 理解演算法的原理、優點與限制

- **這份文件只寫「WHY」**：演算法為什麼這樣設計、假設是什麼、什麼情況會失效（數學、運動學、控制、機率機器人學、路徑規劃、機器人學習）。程式工程與接線不在這裡展開。

- **讀法**：每個階段讀完都回到 AMR-1 上，說得出「這個演算法在 AMR-1 上哪裡用到、什麼條件下會壞掉」。

- **查詢與連結驗證日期**：2026-11-02（Asia/Taipei）。

- **時數全部是推測**，以「CS 碩士、已有 ML 與 PID 理論基礎、Kalman filter 較弱」為起點估計。

## 共同載具：AMR-1

- **AMR-1**：依 [Linorobot2（jazzy branch）](https://github.com/linorobot/linorobot2) 自組的 2WD 差速 AMR。
  - 上位機 Raspberry Pi 4／5（ROS 2 Jazzy），下位機 ESP32 或 Pico 跑 micro-ROS 韌體（[linorobot2_hardware](https://github.com/linorobot/linorobot2_hardware)）。
  - 韌體做輪速 PID，發布輪式里程計 `/odom/unfiltered` 與 `/imu/data`；上位機用 `robot_localization` 的 EKF 融合成 `/odom`，再交給 SLAM Toolbox 與 Nav2。
  - 感測器：輪編碼器、IMU、光達（韌體 wiki 建議新手用 LD19；RPLIDAR C1 也支援。硬體採購細節見 [硬體路線](hardware-learning-path.md)）。

- **為什麼 AMR-1 適合當理論的驗證對象**：一台車上同時有運動學（差速模型）、控制（輪速 PID、路徑追蹤）、估測（EKF、SLAM）、規劃（全域／局部規劃器）四大主題（推測）。

- **選修第二台：SO-101 手臂**，用在 K2 的多關節運動學與 K6 的模仿學習。

## 路線總覽

| 階段 | 內容 | 必修／選修 | 時數（推測） |
| --- | --- | --- | --- |
| K1 | 數學複習：線性代數、機率、微分方程 | 必修 | ~30h |
| K2 | 運動學（剛體運動、正／逆運動學、移動機器人） | 必修 | ~50h |
| K3 | 控制（PID、狀態空間、LQR） | 必修 | ~50h |
| K4 | 機率機器人學（貝氏濾波、KF／EKF／PF、SLAM） | 必修 | ~60h |
| K5 | 路徑規劃（圖搜尋、取樣式規劃） | 必修 | ~30h |
| K6 | 機器人學習：IL／RL／VLA | 選修 | ~60h |

必修合計約 220h（推測）。

## K1 數學複習（必修，~30h）

- **不另排主教材**：CS 碩士已具備線性代數與機率基礎，遇到卡住時回頭查即可（推測）。

- **重點**：旋轉矩陣與座標轉換、特徵值、Jacobian、高斯分佈與共變異數、貝氏定理、一階 ODE 與 Laplace 轉換。

- **能做到**：
  - [ ] 寫出 AMR-1 從 `base_link` 到 `laser` 的齊次轉換矩陣
  - [ ] 解釋為什麼 EKF 要對運動模型取 Jacobian

## K2 運動學（必修，~50h）

- **主教材**：[Modern Robotics（Lynch & Park，免費 PDF + 影片）](https://hades.mech.northwestern.edu/index.php/Modern_Robotics) Ch 2–6（組態空間、剛體運動、正運動學、速度運動學、逆運動學）+ Ch 13（輪式移動機器人）

- **中文對照（擇一）**：
  - [臺大 林沛群「機器人學一」](https://ocw.aca.ntu.edu.tw/courses/mooc0031)
  - [陽明交大 楊谷洋「機器人學」](https://ocw.nycu.edu.tw/?course_page=all-course%2Fcollege-of-electrical-and-computer-engineering%2F%E6%A9%9F%E5%99%A8%E4%BA%BA%E5%AD%B8-robotics-105%E5%AD%B8%E5%B9%B4%E5%BA%A6-%E9%9B%BB%E6%A9%9F%E5%B7%A5%E7%A8%8B%E5%AD%B8%E7%B3%BB-%E6%A5%8A%E8%B0%B7%E6%B4%8B%E8%80%81%E5%B8%AB%2Frobot051_intro)

- **能做到**：
  - [ ] 推導 AMR-1 差速驅動的正／逆運動學（輪速 ↔ 線速度、角速度），並說明非完整約束（nonholonomic）為什麼讓它不能橫移
  - [ ] 解釋輪式里程計為什麼會隨距離累積誤差（打滑、輪徑誤差）
  - [ ] （選）用 Ch 4–6 推 SO-101 的正運動學與數值逆運動學

## K3 控制（必修，~50h）

- **中文主線**：[臺大 林沛群「自動控制」](http://peichunlin.me.ntu.edu.tw/Homepage/AutomaticControl.htm)（課程依 Dorf《Modern Control Systems》Ch 2–9：建模、轉移函數、穩定性、根軌跡、頻率響應）

- **英文補充**：[Steve Brunton Control Bootcamp](https://www.youtube.com/playlist?list=PLMrJAkhIeNNR20Mz-VpzgfQs5zrYi085m)：狀態空間、可控／可觀性、LQR、Kalman filter 的控制觀點

- **能做到**：
  - [ ] 把 AMR-1 單顆輪子的馬達建模成一階系統，解釋 PID 三個增益各自影響什麼（上升時間、超越量、穩態誤差）
  - [ ] 說得出輪速 PID 在低速、電池電壓下降、PWM 飽和時會出什麼問題
  - [ ] 解釋 LQR 與 PID 的取捨，以及 Nav2 路徑追蹤控制器（如 Regulated Pure Pursuit、MPPI）背後的想法（推測）

## K4 機率機器人學（必修，~60h）

- **貝氏濾波入門**：[Kalman and Bayesian Filters in Python（rlabbe，免費 Jupyter 書）](https://rlabbe.github.io/Kalman-and-Bayesian-Filters-in-Python/)（[GitHub](https://github.com/rlabbe/Kalman-and-Bayesian-Filters-in-Python)）：g-h filter → KF → EKF／UKF → particle filter

- **系統課程**：Cyrill Stachniss
  - [Mobile Sensing and Robotics 1（2021/22）](https://www.youtube.com/playlist?list=PLgnQpQtFTOGQEn33QDVGJpiZLi-SlL7vA)
  - [SLAM Course（2013）](https://www.youtube.com/playlist?list=PLgnQpQtFTOGQrZ4O5QzbIHgl3b1JHimN_)：EKF SLAM、FastSLAM、graph-based SLAM

- **參考書（付費，非必要）**：Thrun, Burgard, Fox《Probabilistic Robotics》。Stachniss 的 SLAM 課程大致沿用這本書的架構（推測）。

- **能做到**：
  - [ ] 說明 AMR-1 的 EKF 為什麼要融合輪式里程計與 IMU，各自的雜訊特性與失效情境
  - [ ] 解釋 AMCL（粒子濾波定位）在長走廊、對稱環境為什麼容易失敗
  - [ ] 解釋 SLAM Toolbox 用的 graph-based SLAM：前端 scan matching、後端位姿圖最佳化、loop closure 的作用

## K5 路徑規劃（必修，~30h）

- **主教材**：[LaValle《Planning Algorithms》（免費線上書）](https://lavalle.pl/planning/)：重點讀離散規劃（Ch 2）與取樣式規劃（Ch 5，PRM／RRT）

- **能做到**：
  - [ ] 比較 A\*／Dijkstra 與 RRT 的完備性、最優性、計算量，說得出 AMR-1 在 2D 佔據格地圖上為什麼多用圖搜尋
  - [ ] 解釋全域規劃器與局部控制器的分工，以及 costmap 膨脹半徑設定錯時會發生什麼

## K6 機器人學習：IL／RL／VLA（選修，~60h）

- **中文**：[李宏毅 Deep Reinforcement Learning（2018）](https://www.youtube.com/playlist?list=PLJV_el3uVTsODxQFgzMzPLa16h6B8kWM_)
- **實作入門**：[Hugging Face Deep RL Course](https://huggingface.co/learn/deep-rl-course/unit0/introduction)
- **參考**：[OpenAI Spinning Up](https://spinningup.openai.com/en/latest/)（演算法整理）
- **機器人學習框架**：[LeRobot](https://huggingface.co/docs/lerobot/index)（模仿學習、SO-101）、[Isaac Lab](https://isaac-sim.github.io/IsaacLab/main/index.html)（大規模 RL，需 RTX GPU）
- **進階**：[Underactuated Robotics（MIT, Russ Tedrake）](https://underactuated.csail.mit.edu/index.html)

- **能做到**：
  - [ ] 說明 RL／IL 和 K3–K5 傳統方法的取捨：資料需求、安全性、sim-to-real gap
  - [ ] （選）用 LeRobot 在 SO-101 上錄示範資料並訓練一個模仿學習策略

## 限制／注意

- **時數都是推測**。
- **《Probabilistic Robotics》是付費書**：官方網站查詢時無法連線，所以不附連結；免費路線用 rlabbe + Stachniss 就夠（推測）。
- **林沛群「自動控制」課程頁**：瀏覽器可開，但 curl 直接抓會回 500；開不了時換網路或稍後再試。
- **Stachniss SLAM Course 是 2013 年的錄影**：理論沒過時，但現代 SLAM 實作（如 SLAM Toolbox）細節要另查官方文件。
- **李宏毅 DRL 是 2018 年的課**：適合打基礎，近年的 VLA 與 diffusion policy 不在內。
- **Isaac Lab 需要 RTX 等級 GPU**。

## 來源

- 教科書與課程：Lynch & Park《Modern Robotics》、林沛群（臺大 OCW、個人課程頁）、楊谷洋（陽明交大 OCW）、Steve Brunton、Roger Labbe、Cyrill Stachniss、Steven LaValle、李宏毅、Hugging Face、OpenAI Spinning Up、MIT Underactuated Robotics（連結見各階段）
- 共同載具：[Linorobot2](https://github.com/linorobot/linorobot2)、[linorobot2_hardware](https://github.com/linorobot/linorobot2_hardware)
