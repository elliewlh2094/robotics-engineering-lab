# robotics-engineering-lab

跟著機器人工程師學習路線做練習的統一入口。

這裡是**練習場**，不是作品集。進度會照實紀錄：沒做完就標沒做完，卡住就留下卡住的紀錄。不要為了看起來完整去補空殼或美化進度。

## 現況

這個儲存庫目前只有授權、忽略清單，以及這幾份說明與參考文件。程式練習還沒開始放進來。之後做到哪個階段，再加對應的資料夾或 submodule，不要預先開空資料夾。

預期會陸續出現的內容（做到才加）：

| 路徑 | 用途 |
| --- | --- |
| `docs/` | 三條學習路線的參考文件（給人與程式代理讀） |
| `STATUS.md` | 各階段「能做到」的真實進度 |
| `AGENTS.md` | 給 Claude Code、Codex 等程式代理的約定 |
| 小練習目錄（例如運動學函式庫） | 直接放在本儲存庫 |
| 較完整專案的 submodule | 各自有獨立 Git 歷史；上游專案當依賴安裝，不要整包搬進來 |

## 學習路線參考

- [軟體開發](docs/software-learning-path.md)
- [硬體](docs/hardware-learning-path.md)
- [機器人學知識](docs/robotics-theory-learning-path.md)
- [docs 目錄說明](docs/README.md)

進度請看 [STATUS.md](STATUS.md)。

## 共同練習載具

- **AMR-1**：依 [Linorobot2](https://github.com/linorobot/linorobot2) 自組的 2WD 差速 AMR（主軸）
- **SO-101**：機械手臂（選修第二台）

## Submodule

較完整的練習專案會以 git submodule 掛進來，讓程式代理能在同一個資料夾樹裡看到它們，同時保留各自的 Git 歷史。

第一次完整 clone：

```bash
git clone --recurse-submodules https://github.com/elliewlh2094/robotics-engineering-lab.git
```

已經 clone 過、之後要補齊 submodule：

```bash
git submodule update --init --recursive
```

上游專案（例如 Linorobot2、Nav2）請當依賴安裝，不要整包放進這個儲存庫或當 submodule，除非你真的在改它。你自己寫的設定、外掛與筆記才放進來。

## 授權

MIT。見 [LICENSE](LICENSE)。
