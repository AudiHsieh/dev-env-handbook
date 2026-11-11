# FPGA + PetaLinux 開發環境手冊

適用電腦:Windows 11(使用者 `audi1`)。本手冊只記錄開發環境分工與工具安裝狀態,不含特定專案或板子的設定。

最後更新:2026-11-11

---

## 給 Claude 的規則

1. 開始 FPGA / PetaLinux 相關工作前先讀本手冊。
2. 本手冊列出的工具:有檢查指令的直接檢查,不詢問使用者是否已安裝。
3. 本手冊目前沒有標示「已同意安裝」的項目;缺少的工具安裝前先問使用者。
4. 發現新工具或版本、路徑變更時,更新本手冊並 push。

---

## 1. 環境分工

| 項目 | 執行位置 | 內容 |
|---|---|---|
| PetaLinux | VMware 內 Ubuntu 20.04 VM | petalinux-create/config/build、build script、embedded C 應用與驅動 |
| Vivado / Vitis | 原生 Windows(不在 VM 內) | Verilog/SystemVerilog RTL 存在 Windows 端 Vivado 專案 |

版本與檢查指令:

- Vivado / Vitis / Vitis HLS 2022.2:安裝於 `C:\Xilinx\`(2026-11-11 確認),檢查:`ls /c/Xilinx/Vivado`(Git Bash)
- PetaLinux:版本未記錄(待實測後補)

## 2. 安裝狀態

| 工具 | 狀態 | 檢查指令 | 缺少時 |
|---|---|---|---|
| WSL | 未安裝(2026-09-11 首次確認,2026-11-03 再確認) | `wsl --status`(未安裝時提示執行 `wsl.exe --install`) | 安裝前先問使用者 |
| cocotb / UVM(硬體驗證框架) | 未安裝 | 未記錄 | 安裝前先問使用者 |
