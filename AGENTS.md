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
- **手上的硬體零件**：目前沒有。零件到了只更新這一行；`/practice` 依這一行判斷能不能排實體練習。
- **手上的工具與環境**：三用電表、烙鐵、示波器、3D 印表機都沒有。Ubuntu 24.04 環境沒有（目前的電腦是 Ubuntu 22.04 + ROS 2 Humble，之後會換新電腦）。RTX 等級 GPU 沒有。工具或環境到手時只更新這一行。

## 必守規則

1. **不要灌水進度**。`STATUS.md` 與任何檢查點，只有使用者真的做過才能標成「實際做過」。沒做完就維持「進行中」或「未開始」。
2. **不要把教學範例說成使用者寫的**。來自官方 tutorial、Linorobot2、DiffBot demo、他人 repo 的程式，要明確標出來源。
3. **優先解釋與引導**，讓使用者自己學會。除非使用者明確要求你代寫完整實作，否則不要一次把答案都寫完。
4. **誠實指出弱點**。看到錯誤觀念、不安全的接線／電池用法、或與路線目標脫節的做法，直接說。
5. **小練習放本儲存庫；複雜專案走 submodule；不要 vendor 上游原始碼。**
6. **用台灣繁體中文回覆**（台灣用詞，避免中國大陸用語）。專有名詞、指令、套件名、連結維持英文原文。
7. **路線內容以 `docs/` 為準**；**進度以 `STATUS.md` 為準**。討論某個階段前，先讀對應文件與 STATUS。
8. **`ROADMAP.md` 與進度同步**。修改 `STATUS.md` 的狀態，或修改「手上的硬體零件」「手上的工具與環境」兩行時，在同一個提交裡依 `ROADMAP.md`「更新方式」更新節點狀態與「現在可開始」清單。

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

## 提交規則

### 提交訊息格式

參考 [Conventional Commits](https://www.conventionalcommits.org/)：

```
<類型>: <摘要>

- <變更重點>
- <變更重點>

Co-Authored-By: <代理名稱> <email>
```

- **標題**：`<類型>: <摘要>`。摘要用繁體中文寫出這次提交做了什麼，結尾不加句號，整行不超過 50 個字元。
- **類型**只用以下幾種：
  - `feat`：新增功能，例如新的 skill、腳本。
  - `fix`：修正錯誤或規則漏洞。
  - `docs`：文件與路線內容，例如 `docs/`、`README.md`、`AGENTS.md`、`STATUS.md`。
  - `practice`：`practice/` 下的練習與計畫。
  - `refactor`：不改變行為的重構。
  - `test`：只新增或修改測試。
  - `ci`：GitHub Actions 等持續整合設定。
  - `chore`：其他雜項，例如 `.gitignore`、submodule 指標。
- **內文**：標題後空一行。內文用條列式，每點以 `- ` 開頭，一點只說一件事，寫出改了什麼，必要時寫為什麼。每一點寫成一行，不要手動斷行。
- **共同作者**：程式代理提交時，在內文後空一行，加上該代理的 `Co-Authored-By:` 行。

### 單一提交的範圍

- 一整組變更做完再提交。不要每做一小步就提交，避免歷史變得零碎。
- 一個提交只包含一個可以獨立說明的變更。彼此相關的修改放在同一個提交，例如補 `STATUS.md` 的列，以及讓 skill 讀這些列的規則。彼此無關的修改分成不同提交。
- 標題需要用「和」或「，」串起兩件無關的事時，代表應該拆成兩個提交。
- 每個檔案的修改完整放進一個提交，不要把同一個檔案的修改拆到多個提交。一個檔案涉及多個變更時，把整個檔案歸到最相關的提交，或把這些變更合併成一個提交。
- submodule 內的變更在 submodule 裡提交；父儲存庫更新指標另外提交。
- 修正自己還沒推送的提交時，用 `git commit --amend` 改原提交，不另開新提交。

### 提交與推送

1. 代理完成變更後，先列出要提交的檔案與完整的提交訊息給使用者看。
2. 使用者同意後才提交。使用者修改了訊息時，用使用者修改後的版本。修正提交（amend）也一樣先給使用者看。
3. 推送只由使用者執行。代理不要 `git push`，也不要詢問或建議推送。

## Issues

GitHub Issues 可以當使用者和代理的討論／卡住紀錄，不是給外人看的看板。留下真實問題與嘗試過程即可。

## 進度狀態只允許三種

寫在 `STATUS.md`：

- `未開始`
- `進行中`
- `實際做過`

不要發明「大致完成」「看過就算」這類模糊狀態。
