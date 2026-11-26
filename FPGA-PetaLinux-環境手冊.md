# FPGA + PetaLinux 開發環境手冊

適用電腦:Windows 11(使用者 `audi1`)。本手冊只記錄開發環境分工與工具安裝狀態,不含特定專案或板子的設定。

最後更新:2026-11-26

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

- Vivado / Vitis 2025.2:安裝於 `C:\AMDDesignTools\2025.2\`(2026-11-19 確認),檢查:`ls /c/AMDDesignTools/2025.2/Vivado/bin/vivado.bat /c/AMDDesignTools/2025.2/Vitis/bin/xsdb.bat`(Git Bash)
  - 目錄結構與 2022.2 相反:版本目錄在上層,`<安裝根>\2025.2\Vivado\bin`、`<安裝根>\2025.2\Vitis\bin`(2022.2 為 `C:\Xilinx\Vivado\2022.2\bin`)。寫腳本找工具時兩種結構要分開處理。
  - xsdb 在 `Vitis\bin\xsdb.bat`(`Vivado\bin\xsdb.bat` 也有);`[version]` 回傳 `2025.2`,隨附 hw_server v2025.2(2026-11-19 實測 `connect; targets` 可執行)。
  - `C:\Xilinx\2025.2\Vitis\` 只有空的 `bin`,不是完整安裝,不要用。
- Vivado / Vitis / Vitis HLS 2022.2:安裝於 `C:\Xilinx\`(2026-11-11 確認),檢查:`ls /c/Xilinx/Vivado`(Git Bash)
- PetaLinux:版本未記錄(待實測後補)

2022.2 → 2025.2 版本差異(本機安裝檔目錄比對,2026-11-19):

| 項目 | 2022.2 | 2025.2 | 備註 |
|---|---|---|---|
| Aurora 64B66B IP | v12.0 | v13.0 | 依 13.0 instantiation template 比對,master/slave port 名稱與 v12.0 相同 |
| VIO IP | v3.0 | v3.0 | 不變 |
| lwIP 函式庫(embeddedsw) | `lwip211`(v1.5~v1.8) | `lwip220`(v1.0~v1.3) | lwIP 2.1.1 → 2.2.0;BSP 設定名稱是否改動未確認 |
| XSCT | 主要腳本工具 | 仍隨附(`Vitis\bin\xsct.bat`),啟動時提示改用 Python CLI(`vitis -i` 互動、`vitis -s <script>` 腳本) | 舊 xsct 腳本在 2025.2 是否完全相容未確認 |
| xsdb | `Vitis\2022.2\bin\xsdb.bat` | `2025.2\Vitis\bin\xsdb.bat` | 用法不變 |

已知限制:

- Vivado / Vitis(xsct)/ xsdb 2022.2 不支援含中文的路徑(2026-11-11 實測:Vivado `Failed to open zip archive ...hwdef`)。專案放在中文路徑下時,build 與 xsdb 下載改在 ASCII 目錄(例:`%TEMP%\zcu102_build`)進行,產出再拷回專案。
- Python 2 工具（例：AMD BoardUI）遇到中文名稱的 COM port（藍牙序列埠）會 UnicodeEncodeError：先在裝置管理員停用或移除該 port。

## 2. 安裝狀態

| 工具 | 狀態 | 檢查指令 | 缺少時 |
|---|---|---|---|
| WSL | 未安裝(2026-09-11 首次確認,2026-11-03 再確認) | `wsl --status`(未安裝時提示執行 `wsl.exe --install`) | 安裝前先問使用者 |
| cocotb / UVM(硬體驗證框架) | 未安裝 | 未記錄 | 安裝前先問使用者 |
| Tera Term(序列終端機,板測主控台截圖用) | 已安裝 5.7.1(2026-11-20,使用者同意;winget `TeraTermProject.teraterm`),路徑 `%LOCALAPPDATA%\Programs\teraterm5\`(`ttermpro.exe`、巨集 `ttpmacro.exe`) | `Test-Path "$env:LOCALAPPDATA\Programs\teraterm5\ttermpro.exe"` | `winget install --id TeraTermProject.teraterm -e` |
