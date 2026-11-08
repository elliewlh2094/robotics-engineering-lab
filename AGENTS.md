# 給程式代理的約定

Claude Code 請先讀這份文件。若你是從 `CLAUDE.md` 進來的，內容以本檔為準。

## 這個儲存庫是什麼

`robotics-engineering-lab` 是使用者跟著學習路線做練習時的**統一入口與練習場**，方便和 Claude Code、Codex 等程式代理在同一個上下文討論。

- 這**不是**作品集或展示空間。
- 目標是誠實紀錄真實能力，而不是看起來很厲害。
- 小練習直接放在本儲存庫；較完整的練習專案用 git submodule。
- 上游專案（Linorobot2、Nav2、SO-101、Crazyflie、PX4、mini_pupper_ros、CHAMP 官方 repo 等）當依賴安裝，不要整包搬進來。只有使用者自己改過或新寫的設定、外掛、筆記才放進這裡或自己的 submodule。

## 使用者背景

- CS 碩士生。
- **已有**：Python、ROS 2 基礎（沒用過 Nav2、MoveIt、`ros2_control`）、Gazebo、PID 理論、手動飛無人機。
- **缺口**：C/C++ 幾乎從零、電子／電路沒碰過、沒組過實體機器人、沒碰過 LiPo、Kalman filter 較弱。

## 必守規則

1. **不要灌水進度**。`STATUS.md` 與任何檢查點，只有使用者真的做過才能標成「實際做過」。沒做完就維持「進行中」或「未開始」。
2. **不要把教學範例說成使用者寫的**。來自官方 tutorial、Linorobot2、DiffBot demo、他人 repo 的程式，要明確標出來源。
3. **優先解釋與引導**，讓使用者自己學會。除非使用者明確要求你代寫完整實作，否則不要一次把答案都寫完。
4. **誠實指出弱點**。看到錯誤觀念、不安全的接線／電池用法、或與路線目標脫節的做法，直接說。
5. **小練習放本儲存庫；複雜專案走 submodule；不要 vendor 上游原始碼。**
6. **用台灣繁體中文回覆**（台灣用詞，避免中國大陸用語）。專有名詞、指令、套件名、連結維持英文原文。
7. **路線內容以 `docs/` 為準**；**進度以 `STATUS.md` 為準**。討論某個階段前，先讀對應文件與 STATUS。

## 學習路線文件

| 檔案 | 內容 |
| --- | --- |
| [docs/software-learning-path.md](docs/software-learning-path.md) | 怎麼寫：C++／Python、ROS 2、Nav2、模擬、工程習慣 |
| [docs/hardware-learning-path.md](docs/hardware-learning-path.md) | 怎麼接、怎麼組：電路、MCU、馬達、整機、工業通訊 |
| [docs/robotics-theory-learning-path.md](docs/robotics-theory-learning-path.md) | 為什麼：運動學、控制、機率機器人學、規劃 |

三條路線共用載具 **AMR-1**（Linorobot2 2WD），選修第二台是 **SO-101**，選修多旋翼是 **Crazyflie 2.1+** 與 **PX4 的 Holybro X500**（先模擬 `gz_x500`；實體 X500 V2 與焊接練習 ESP-FLY 見各路線），選修四足是 **Mini Pupper 2**。

## 怎麼跟 submodule 一起工作

- 每個 submodule 是**獨立的 Git 儲存庫**。在 submodule 目錄裡 commit、push，改的是那個專案自己的歷史。
- 父儲存庫只記錄「目前指到哪個 commit」。子專案有新 commit 並 push 之後，回到父儲存庫更新指標再 commit 一次。
- 不要在父儲存庫裡直接改 submodule 內的檔案卻忘記進子目錄 commit。
- clone 時請用 `git clone --recurse-submodules`；已存在的工作複本用 `git submodule update --init --recursive`。

## Issues

GitHub Issues 可以當使用者和代理的討論／卡住紀錄，不是給外人看的看板。留下真實問題與嘗試過程即可。

## 進度狀態只允許三種

寫在 `STATUS.md`：

- `未開始`
- `進行中`
- `實際做過`

不要發明「大致完成」「看過就算」這類模糊狀態。
