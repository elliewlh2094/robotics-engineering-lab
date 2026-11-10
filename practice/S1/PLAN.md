# S1 路徑計畫

- 路線：[docs/software-learning-path.md](../../docs/software-learning-path.md) 「S1 程式基礎（必修，~100h）」
- 長期目錄：提議 `diff_drive_kinematics/`（放在儲存庫根目錄），待使用者確認。S2 加 CMake／GTest／Docker／CI、S3 包成 ROS adapter，都沿用這個目錄。之後是否改成 submodule，依 `AGENTS.md` 規則 5 由使用者決定。

## 成功條件

| STATUS.md 原文 | 改寫後的成功條件 | 驗收方式 |
| --- | --- | --- |
| C++17 差速運動學類別，Ubuntu 24.04 可編譯，有乾淨 Git 歷史 | (a) 一個類別以輪半徑 `r` 與輪距 `L` 建構，提供正運動學（左右輪角速度 rad/s → 線速度 `v` m/s、角速度 `ω` rad/s）與逆運動學（`v`、`ω` → 左右輪角速度）。逆運動學接正運動學後回到原值，誤差 ≤ 1e-9。<br>(b) 一個位姿積分函式：輸入目前位姿 `(x, y, θ)`、`v`、`ω`、`dt`，輸出新位姿；`θ` 包到 (−π, π]。直線、原地旋轉、等半徑圓三種情境符合代理提供的期望值。<br>(c) 只用 C++17 標準函式庫，在 Ubuntu 24.04 用 `g++ -std=c++17 -Wall -Wextra -Wpedantic -Werror` 編譯，沒有警告。<br>(d) 長期目錄的每一個 commit 都能編譯，並通過該 commit 當時的測試；儲存庫不追蹤建置產物；每個 commit 只做一件事，標題不超過 72 字元（硬上限，`check_history.sh` 只檢查這一條；50 字元是 AGENTS.md 的目標，只提醒不擋）並說明改了什麼。 | (a)(b)(c)：代理提供的 `build_and_test.sh`<br>(d)：代理提供的 `check_history.sh` |
| Robotisim §5 觀念表各做一題（const／RAII／unique_ptr／virtual／chrono） | 依 Robotisim §5.2 表格 "Small exercise" 欄的原文各做一題，都放進長期目錄：<br>(1) `const`："Pass a pose into a calculation without mutating it"。位姿積分函式以 `const Pose&` 接收輸入；測試以 `const` 物件呼叫。<br>(2) RAII："Wrap a mock device connection in an owning object"。建構時開啟連線、解構時關閉；正常離開 scope 與因例外離開 scope，開啟與關閉次數都相等；物件不可複製。<br>(3) `unique_ptr`："Explain which object owns a driver"。一個擁有者類別以 `std::unique_ptr` 持有 driver；不可複製、可移動（以 `static_assert` 檢查）。<br>(4) virtual："Substitute a fake hardware interface in a test"。定義抽象的 driver 介面（含 virtual 解構子）；測試以 fake 實作替換，並檢查 fake 收到的輪速命令。<br>(5) `std::chrono`："Implement a command-age test with an injected clock"。命令看門狗透過注入的時鐘判斷命令年齡；測試用 fake clock 推進時間，不呼叫 `sleep`；年齡 < timeout 時回傳命令，年齡 ≥ timeout 時回傳空的 `std::optional`。 | 五題都用 `build_and_test.sh`。<br>(1)(3) 另加 ANSWER.md 追問：(1) 為何用 `const&` 而不是傳值或非 `const` 參考；(3) 誰擁有 driver、移動後原物件處於什麼狀態、為何不用 `shared_ptr`。<br>(5) 另加 ANSWER.md 追問：空的 `std::optional` 由誰決定變成停車命令（Robotisim §5.3："Silence is not automatically a stop command"）。 |
| Python 腳本把運動學輸出畫成圖 | 一支 C++ 小程式把位姿積分結果輸出成 CSV（欄位：`t, x, y, theta, v, omega`）。一支 Python 腳本讀這個 CSV，畫出 XY 軌跡與 `v`／`ω` 對時間的圖，存成 PNG。直線、等半徑圓、原地旋轉三種情境各產生一張圖。 | 指令：腳本退出碼為 0 且產生 3 個 PNG。<br>ANSWER.md 追問：每張圖的形狀為何符合輸入的輪速，以及圓軌跡的半徑如何由 `v`、`ω` 算出。 |

假設：
- 「運動學類別」除了輪速 ↔ `v`／`ω`，也包含位姿積分。理由：Robotisim §5.2 的 `const` 題需要一個位姿；S2 的 GTest 要測「角度 wrap」與「dt 改變」，代表 S1 的函式庫已經有位姿積分。
- **Ubuntu 24.04**：本機是 Ubuntu 22.04.5、g++ 11.4.0，沒有安裝 Docker。開發階段先在本機以 `-std=c++17` 編譯；24.04 的驗證放在步驟 9，需要一個 24.04 環境（另一台電腦、VM，或提早借學 S2 的 Docker）。由使用者選擇。
- S1 還沒學 CMake 與 GTest。代理提供以 `g++` 編譯的 shell 腳本，以及只用 `assert` 風格巨集的簡易測試程式，都標「代理提供」。使用者只寫被測的程式。
- 五題觀念題都放進長期目錄，和運動學類別同一個函式庫，因為 S2 的 timeout 測試與 S3 的 ROS adapter 會用到 driver 介面與看門狗。
- AMR-1 的輪半徑與輪距由建構子參數傳入。實際數值在展開步驟 2 時從 Linorobot2 的設定查證，查不到就標「（推測）」。
- Python 腳本只畫圖，不另外用 Python 重寫運動學。「Python 版與 C++ 版用 tolerance 比對」屬於 S6 的 profile，不在 S1 範圍。
- 「乾淨 Git 歷史」從步驟 2 開始逐步練習：每一步都在分支上 commit，先跑 `check_history.sh` 再合併。已合併進 `main` 的 commit 不改寫。

## 依賴

- 步驟 2 需要差速正／逆運動學公式（來自步驟 1，借學：K2 差速運動學）
- 步驟 3 需要步驟 2 的類別
- 步驟 4、5、7 需要步驟 2 的編譯與測試流程
- 步驟 6 需要步驟 4（RAII 觀念）與步驟 5（driver 介面）
- 步驟 7 需要 `std::optional`（learncpp 12.15，於步驟 7 先讀）
- 步驟 8 需要步驟 3 的位姿積分
- 步驟 9 需要步驟 2–8 的全部 commit，以及一個 Ubuntu 24.04 環境

## 步驟

### 1. 推導差速正／逆運動學 — 未開始
- 目標：寫出輪速 ↔ `v`／`ω` 的公式，並用代理提供的 Python 驗算腳本，以數值例子檢查自己寫的 Python 函式。
- 先讀：[Modern Robotics](https://hades.mech.northwestern.edu/index.php/Modern_Robotics)（[PDF](https://hades.mech.northwestern.edu/images/2/25/MR-v2.pdf)）§13.3.1 Modeling
- 前置：借學：K2 差速運動學（只學到正／逆運動學公式，非完整約束留給 K2）
- 練習：

### 2. 第一個 C++17 類別：正／逆運動學 — 未開始
- 目標：運動學類別通過 `build_and_test.sh`（條件 a、c 的本機部分），並完成長期目錄的第一批 commit。
- 先讀：[learncpp](https://www.learncpp.com/) 0.7、0.11、0.12、2.8、2.11、14.2–14.4、14.9；[Missing Semester 2026](https://missing.csail.mit.edu/2026/version-control/) Version Control and Git
- 前置：步驟 1
- 練習：

### 3. 位姿積分與 `const Pose&` — 未開始
- 目標：位姿積分在直線、原地旋轉、等半徑圓三種情境通過測試，`θ` 正確包到 (−π, π]（條件 b、觀念題 1）。
- 先讀：[learncpp](https://www.learncpp.com/) 12.4、12.6
- 前置：步驟 2
- 練習：

### 4. RAII：mock 裝置連線 — 未開始
- 目標：mock 連線物件在正常離開與例外離開 scope 時都關閉連線，且不可複製（觀念題 2）。
- 先讀：[learncpp](https://www.learncpp.com/) 15.4 Introduction to destructors
- 前置：步驟 2
- 練習：

### 5. virtual 介面：用 fake driver 測試 — 未開始
- 目標：抽象 driver 介面與 fake 實作；測試確認 fake 收到逆運動學算出的輪速命令（觀念題 4）。
- 先讀：[learncpp](https://www.learncpp.com/) 25.2、25.4、25.7
- 前置：步驟 2
- 練習：

### 6. `unique_ptr`：誰擁有 driver — 未開始
- 目標：擁有者類別以 `std::unique_ptr` 持有 driver，`static_assert` 確認不可複製、可移動，並在 ANSWER.md 說明擁有權（觀念題 3）。
- 先讀：[learncpp](https://www.learncpp.com/) 22.1、22.4、22.5
- 前置：步驟 4、5
- 練習：

### 7. `std::chrono`：注入時鐘的命令看門狗 — 未開始
- 目標：看門狗用 fake clock 測試命令年齡，逾時回傳空的 `std::optional`，測試不呼叫 `sleep`（觀念題 5）。
- 先讀：[learncpp](https://www.learncpp.com/) 12.15 std::optional、18.4 Timing your code；[cppreference: std::chrono::steady_clock](https://en.cppreference.com/w/cpp/chrono/steady_clock)
- 前置：步驟 2
- 練習：

### 8. CSV 輸出與 Python 畫圖 — 未開始
- 目標：C++ 小程式輸出三種情境的 CSV，Python 腳本畫成 3 個 PNG，並在 ANSWER.md 說明圖形。
- 先讀：[Matplotlib Pyplot tutorial](https://matplotlib.org/stable/tutorials/pyplot.html) Introduction to pyplot
- 前置：步驟 3
- 練習：

### 9. Ubuntu 24.04 乾淨 clone 驗證 — 未開始
- 目標：在 Ubuntu 24.04 環境從乾淨 clone 執行 `check_history.sh`，每個 commit 都能編譯並通過測試（條件 c 的 24.04 部分、條件 d）。
- 先讀：依使用者選的 24.04 環境決定（展開時補）
- 前置：步驟 2–8
- 練習：

## docs/ 修改建議

- S1 的第一個「能做到」需要差速正／逆運動學，但這個知識在知識路線 K2，S1 章節沒有寫出這個前置條件。建議在 S1 加一句「運動學公式見知識路線 K2；先做 K2 前兩列，或借學到公式即可」。
- S1 寫「對照 Robotisim §5 的觀念表」。2026-11-09 查證時，該表格位於 §5.2 "Learn modern C++ as a systems tool"，原表有 6 列，S1 只取其中 5 列（略過 "Containers and allocation behavior"）。建議把章節寫成 §5.2，並註明略過哪一列。
- S1 要求「在 Ubuntu 24.04 編譯」，但 S1 還沒學 Docker（在 S2）。若使用者的主機不是 24.04，S1 沒有教材說明如何取得 24.04 環境。建議在 S1 或「限制／注意」補一句說明。

## 調整紀錄

- 2026-11-09：建立骨架，待使用者審查。
