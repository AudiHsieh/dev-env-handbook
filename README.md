# dev-env-handbook

AudiHsieh 的開發環境手冊。Claude 在每個專案開始前先讀這裡，已記載的工具直接檢查與使用，不再重複詢問安裝。

- [STM32 + VSCode 開發環境手冊](STM32-VSCode-環境手冊.md)
- [FPGA + PetaLinux 開發環境手冊](FPGA-PetaLinux-環境手冊.md)

## Windows 共通（所有專案）

- Windows 主控台預設 cp950：用 `Start-Process powershell` 開新視窗跑會輸出中文（UTF-8）的 Python／腳本時，`-Command` 先執行 `[Console]::OutputEncoding = [System.Text.Encoding]::UTF8`，再直接 `& .\script.ps1 | Tee-Object -FilePath <檔>`；不要用巢狀 `powershell -File script.ps1 | Tee-Object`（外層以 cp950 解碼 → 亂碼）。PowerShell 5.1 的 `Tee-Object` 存檔為 UTF-16，讀取時需轉碼（例：`iconv -f utf-16 -t utf-8`）。（2026-11-11 實測）
