# 學習路線參考文件

這份目錄整理三條練習路線，給人與程式代理一起讀。內容由使用者的 Heptabase 筆記轉換而來（轉換日期：2026-11-06，Asia/Taipei）。連結的查核日期寫在各文件內文。

| 文件 | 負責的一面 | 必修時數（推測） |
| --- | --- | --- |
| [software-learning-path.md](software-learning-path.md) | 怎麼寫（HOW TO WRITE） | 約 410h |
| [hardware-learning-path.md](hardware-learning-path.md) | 怎麼接、怎麼組（HOW TO WIRE） | 約 140h |
| [robotics-theory-learning-path.md](robotics-theory-learning-path.md) | 為什麼（WHY） | 約 220h |

## 怎麼切分

同一個主題可以出現在不同文件，但各文件只寫自己負責的那一面：

- 控制、估測、運動學的**數學與假設** → 知識路線
- 同一主題的**程式、框架、測試、CI** → 軟體路線
- 同一主題的**接線、電源、組裝、量測** → 硬體路線
- 多旋翼的**飛行控制程式與 ROS 2** → 軟體路線；**焊接、電池、動力接線** → 硬體路線；**串級控制與 extended Kalman filter 的假設** → 知識路線
- 四足的**步態與 ROS 2** → 軟體路線；**組裝、伺服校正、電池** → 硬體路線；**腿部運動學、步態相位與靜態平衡** → 知識路線

## 共同載具

- **AMR-1**：Linorobot2 2WD 差速 AMR（三條路線的主軸）
- **SO-101**：機械手臂（選修）
- **多旋翼（選修）**：Crazyflie 2.1+ 是第一台，接著是 PX4 的 Gazebo 模型 `gz_x500`（Holybro X500）。實體 X500 V2 與焊接用的 ESP-FLY 見各路線。不取代 AMR-1。必修時數不變。
- **四足（選修）**：Mini Pupper 2。不取代 AMR-1。必修時數不變。

進度請回上層看 [STATUS.md](../STATUS.md)。給程式代理的約定見 [AGENTS.md](../AGENTS.md)。
