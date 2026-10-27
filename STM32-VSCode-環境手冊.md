# STM32 + VSCode 開發環境手冊

適用電腦：Windows 11（使用者 `audi1`）。本手冊只記錄**開發需要的工具**與其檢查/安裝方式，不含任何特定板子或晶片的設定（腳位、時脈、DFP 版本等屬於各專案自己的文件）。

最後更新：2026-10-27

---

## 給 Claude 的規則

1. 開始任何專案前先讀本手冊。
2. 本手冊列出的工具：**直接用「檢查指令」確認，不要詢問使用者是否已安裝**。
3. 檢查結果缺少時：標示「已同意安裝」的項目**直接執行安裝指令**，裝完再回報；其餘項目才詢問。
4. 預設開發選擇（見最後一節）直接採用，不再詢問；使用者另有指定時以使用者為準。
5. 發現新工具或版本、路徑變更時，更新本手冊並 push。

---

## 1. 編輯器與擴充套件

| 工具 | 已裝版本 | 檢查指令 | 缺少時 |
|---|---|---|---|
| VS Code | 1.141.0 | `code --version` | 已同意安裝：`winget install -e --id Microsoft.VisualStudioCode` |
| STM32Cube for VS Code | 3.11.0 | `code --list-extensions \| findstr stmicroelectronics` | 已同意安裝：`code --install-extension stmicroelectronics.stm32-vscode-extension` |
| CMake Tools | 1.24.42 | `code --list-extensions \| findstr cmake-tools` | 已同意安裝：`code --install-extension ms-vscode.cmake-tools` |
| C/C++ Extension Pack | 1.5.1 | `code --list-extensions \| findstr cpptools` | 已同意安裝：`code --install-extension ms-vscode.cpptools-extension-pack` |
| Serial Monitor | 2.0.0 | `code --list-extensions \| findstr serial-monitor` | 已同意安裝：`code --install-extension eclipse-cdt.serial-monitor` |

STM32Cube for VS Code 會一併帶入 ST 的 debug / clangd / registers / RTOS 等子套件，並由 **bundles manager** 下載下一節的工具鏈。

## 2. 韌體工具鏈（STM32Cube bundles）

位置：`%LOCALAPPDATA%\stm32cube\bundles\<名稱>\<版本>\`。由 STM32Cube for VS Code 自動管理，**不要另外手動加進 PATH**。

| Bundle | 已裝版本 | 用途 |
|---|---|---|
| gnu-tools-for-stm32 | 14.3.1+st.2 | arm-none-eabi-gcc 交叉編譯器 |
| gnu-gdb-for-stm32 | 14.3.1+st.2 | arm-none-eabi-gdb |
| cmake | 4.3.1+st.1 | 韌體建置用 CMake |
| ninja | 1.13.2+st.1 | 韌體建置用 Ninja |
| programmer | 2.23.0 | STM32CubeProgrammer CLI（燒錄） |
| stlink-gdbserver | 7.14.0+st.2 | ST-Link 除錯 |
| stlink-server / stlink-usb-driver / stlink-upgrader | — | ST-Link 驅動與韌體更新 |
| pack-manager | — | 晶片 DFP 下載（依專案晶片自動取得） |

檢查指令（PowerShell）：

```powershell
Get-ChildItem "$env:LOCALAPPDATA\stm32cube\bundles" -Directory | ForEach-Object { "$($_.Name): $((Get-ChildItem $_.FullName -Directory | Select-Object -ExpandProperty Name) -join ', ')" }
```

缺少時（已同意安裝）：在 VS Code 開啟 STM32Cube 面板 → Bundles Manager 安裝對應 bundle；或打開任一 STM32 CMake 專案，擴充套件會提示補齊。

## 3. STM32CubeMX

| 項目 | 內容 |
|---|---|
| 位置 | `%LOCALAPPDATA%\Programs\STM32CubeMX\STM32CubeMX.exe` |
| 檢查指令 | `Test-Path "$env:LOCALAPPDATA\Programs\STM32CubeMX\STM32CubeMX.exe"` |
| 缺少時 | 需登入 ST 帳號下載，**詢問使用者**：https://www.st.com/en/development-tools/stm32cubemx.html |

可用 script 模式免 GUI 產生專案：

```powershell
& "$env:LOCALAPPDATA\Programs\STM32CubeMX\STM32CubeMX.exe" -q <script.txt>
```

script 內常用指令：`load <MCU>`、`config load <.ioc>`、`project toolchain CMake`、`project path <dir>`、`project name <name>`、`project generate`、`exit`。

## 4. 電腦端（Host）編譯器 —— 單元測試用

| 工具 | 已裝版本 | 檢查指令 | 缺少時 |
|---|---|---|---|
| WinLibs MinGW-w64 gcc（POSIX, UCRT） | gcc 16.2.0 | `gcc --version` | 已同意安裝：`winget install -e --id BrechtSanders.WinLibs.POSIX.UCRT --accept-source-agreements --accept-package-agreements --silent` |

- 位置：`%LOCALAPPDATA%\Microsoft\WinGet\Packages\BrechtSanders.WinLibs.POSIX.UCRT_Microsoft.Winget.Source_8wekyb3d8bbwe\mingw64\bin`（已加入使用者 PATH；剛裝完需重開 shell / VS Code 才生效）。
- WinLibs 自帶 `cmake`（4.4.4）、`ctest`、`ninja`、`gdb`、`cppcheck`。**Host 測試用這套；韌體建置用第 2 節的 ST bundle**，兩者互不取代。
- 若目前 shell 尚未生效，可暫時：
  ```powershell
  $env:Path = "$env:LOCALAPPDATA\Microsoft\WinGet\Packages\BrechtSanders.WinLibs.POSIX.UCRT_Microsoft.Winget.Source_8wekyb3d8bbwe\mingw64\bin;$env:Path"
  ```

## 5. 硬體除錯器

| 工具 | 狀態 |
|---|---|
| ST-Link（V2） | 使用者有。驅動由 `stlink-usb-driver` bundle 提供 |

檢查：接上後 `STM32_Programmer_CLI -l st`（位於 programmer bundle 的 `bin` 下）應列出探針。

## 6. 版本控制與其他

| 工具 | 已裝版本 | 檢查指令 | 缺少時 |
|---|---|---|---|
| Git | 2.55.0 | `git --version` | 已同意安裝：`winget install -e --id Git.Git` |
| GitHub CLI | 2.101.0（已登入 `AudiHsieh`） | `gh auth status` | 已同意安裝：`winget install -e --id GitHub.cli`；登入需使用者執行 `gh auth login` |
| Python | 3.13.15 | `python --version` | 已同意安裝：`winget install -e --id Python.Python.3.13` |
| Poppler（pdftotext 等） | 25.07.0 | `pdftotext -v` | 已同意安裝：`winget install -e --id oschwartz10612.Poppler` |

## 7. 預設開發選擇（直接採用，不再詢問）

- 韌體框架：**STM32CubeMX 產生 HAL + CMake 專案**，在 VS Code 以 STM32Cube for VS Code 建置。
- 燒錄 / 除錯：**ST-Link SWD**，透過 STM32Cube for VS Code 的 debug 設定。
- 串口觀察：VS Code **Serial Monitor**。
- 電腦端單元測試：**Unity（ThrowTheSwitch）+ CMake/CTest**，以 WinLibs gcc 編譯，在 VS Code Testing / CMake Tools 面板執行；硬體相依部分以假實作替換。
- 每個專案根目錄 `git init`。
