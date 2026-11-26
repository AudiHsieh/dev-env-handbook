# dev-env-handbook

AudiHsieh 的開發環境手冊。Claude 在每個專案開始前先讀這裡，已記載的工具直接檢查與使用，不再重複詢問安裝。

- [STM32 + VSCode 開發環境手冊](STM32-VSCode-環境手冊.md)
- [FPGA + PetaLinux 開發環境手冊](FPGA-PetaLinux-環境手冊.md)

## Windows 共通（所有專案）

- Windows 主控台預設 cp950：用 `Start-Process powershell` 開新視窗跑會輸出中文（UTF-8）的 Python／腳本時，`-Command` 先執行 `[Console]::OutputEncoding = [System.Text.Encoding]::UTF8`，再直接 `& .\script.ps1 | Tee-Object -FilePath <檔>`；不要用巢狀 `powershell -File script.ps1 | Tee-Object`（外層以 cp950 解碼 → 亂碼）。PowerShell 5.1 的 `Tee-Object` 存檔為 UTF-16，讀取時需轉碼（例：`iconv -f utf-16 -t utf-8`）。（2026-11-11 實測）
- 改既有文字檔要保留原行尾：優先用 Edit 工具；Git Bash 的 sed -i 會把 CRLF 轉成 LF，要用 sed -b -i；Python 文字模式寫檔會把 LF 轉成 CRLF，要用 open(..., newline='') 或二進位讀寫。（2026-11-24 實測）
- PowerShell 5.1 讀無 BOM 的 UTF-8 .ps1 會用系統碼頁解析，含中文會語法錯：含非 ASCII 的 .ps1 存成 UTF-8 with BOM。
- 截圖程式（PrintWindow／CopyFromScreen）先呼叫 SetProcessDPIAware，否則縮放 >100% 時只截到左上角；截圖前按 Esc 關 Windows 11 Click to Do 浮層、縮小擋住目標的視窗（含 Claude 桌面 app）。
