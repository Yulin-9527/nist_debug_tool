# NIST Debug 瑞士刀 (NIST Debug Swiss Army Knife)

[![Python Version](https://img.shields.io/badge/python-3.8%2B-blue.svg)](https://www.python.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Version](https://img.shields.io/badge/version-v2.0-green.svg)]()

一個為開發者、韌體工程師與資安研究員設計的桌面小工具，旨在簡化處理 BIOS/UEFI 韌體或其他二進位檔案時的常見任務。整合四大核心功能：**Hash 計算器**、**RSA 簽章解密**、**Bin 檔案切割器** 與 **PQC ML-DSA 簽章驗證**。

---

## 功能特色 (Features)

本工具提供四大模組，可透過頂部「工具」選單隨時切換。

---

### 1. 🧮 Hash 計算器 (Hash Calculator)

計算整個檔案或指定區塊的雜湊值。

- **支援演算法**：`SHA-256`、`SHA-384`、`SHA-512`、`MD5`、`CRC32`
- **區域計算**：可指定**起始位址 (Offset)** 與**大小 (Size)** 計算特定區塊，留空大小則讀取至檔尾
- **支援拖曳**：直接將檔案拖入視窗即可操作

---

### 2. 🔐 RSA 簽章解密 (RSA Signature Decryption)

以公鑰還原 RSA 簽章原文，常用於分析韌體驗證流程。

- **公鑰格式**：支援 PEM 格式與 `.bin` 二進位模數格式
- **金鑰長度**：支援 `RSA-2048` 與 `RSA-4096`
- **Byte Order 控制**：
  - 公鑰支援 Big-endian（標準）與 Little-endian（反序）
  - 簽章資料同樣支援 Big-endian / Little-endian 切換
- **智慧解析**：自動偵測 PKCS#1 v1.5 Padding，並從 ASN.1 DigestInfo 結構提取 Hash 值

---

### 3. ✂️ Bin 檔案切割器 (Binary File Splitter)

從大型二進位檔案中提取指定區塊。

- **精準切割**：指定起始位址與大小即可提取
- **即時預覽**：儲存前可先預覽切割內容（Hex 格式）
- **自動命名**：未指定檔名時，依來源檔名、起始位址與大小自動產生

---

### 4. 🔏 PQC ML-DSA 簽章驗證 (Post-Quantum Cryptography Verifier)

針對後量子密碼學（PQC）標準 FIPS 204 的 ML-DSA 簽章驗證工具，用於驗證韌體 bin 檔案中的 PQC 簽章。

#### 驗證流程

```
message  = bin[msg_start : msg_start + msg_size]
hash     = SHA-256 / SHA-384 / SHA-512 (message)
result   = ML-DSA.verify(pubkey, hash, signature)
```

#### 主要功能

- **單檔操作**：只需一個 bin 檔案，透過偏移量指定明文區塊、公鑰、簽章的位置
- **演算法支援**：`ML-DSA-44`、`ML-DSA-65`、`ML-DSA-87`（對應 NIST FIPS 204）
- **Hash 模式**：`SHA-256`、`SHA-384`、`SHA-512`
- **自動偵測大小**：依所選演算法自動計算公鑰與簽章的結束位址並顯示
- **多格式大小輸入**：大小欄位支援 `0x30000`、`196608`、`192kb`、`1mb` 等格式

#### Debug 模式

勾選「顯示 ML-DSA 內部中間值」後，額外顯示 FIPS 204 Algorithm 8 的完整驗證路徑：

| 符號 | 說明 |
|------|------|
| `rho` | 公鑰種子，用於展開矩陣 A |
| `c~` | 簽章中的 challenge hash |
| `z` | Response 向量（各 polynomial 前 8 個係數 + inf-norm）|
| `h` | Hint 向量（sum_hint 與 ω 比較）|
| `tr` | 公鑰的 SHAKE-256 digest |
| `μ (mu)` | 由 tr 與 message 計算的訊息摘要 |
| `c` | 從 c~ 取樣的 challenge polynomial（tau、non-zero 係數數）|
| `Az - ct1` | 矩陣運算中間結果 |
| `w'` | UseHint 重建的 commitment |
| `c~ match` | 驗證端重算 c~ 與簽章 c~ 是否一致 |

正規性檢查也一併顯示：
- `norm check`：z 向量 inf-norm 是否小於 γ1 - β
- `hint check`：hint 向量 sum_hint 是否不超過 ω

---

## 安裝需求 (Requirements)

```bash
pip install PyQt6 cryptography pqcrypto dilithium-py
```

| 套件 | 用途 |
|------|------|
| `PyQt6` | GUI 框架 |
| `cryptography` | RSA 簽章解密 |
| `pqcrypto` | ML-DSA 驗證（PQClean C 實作） |
| `dilithium-py` | ML-DSA debug 模式（純 Python，提供中間值存取）|

---

## 下載與使用 (Download & Usage)

1. 前往本專案的 **Releases 頁面**
2. 下載最新版本的 `NistDebugTool.exe`
3. 無需安裝，直接執行

或從原始碼執行：

```bash
py main.py
```

---

## 操作說明 (Instructions)

### Hash 計算器

1. 拖曳檔案至視窗，或點擊「瀏覽」選擇檔案
2. （可選）在「讀取設定」填入起始位址與大小；支援十進位（`12288`）或十六進位（`0x3000`）
3. 選擇 Hash 演算法
4. 點擊「開始計算 Hash」

### RSA 簽章解密

1. 切換到「RSA 簽章解密」頁面
2. 分別選擇「RSA 簽章檔案」與「RSA 公鑰檔案」（支援拖曳）
3. 選擇金鑰長度（2048 / 4096）
4. 若公鑰為 `.bin` 格式，選擇正確的 Byte Order（UEFI 韌體通常為 Little-endian）
5. 點擊「解密簽章」

### Bin 檔案切割器

1. 切換到「切割 Bin 檔案」頁面
2. 選擇或拖曳 bin 檔案
3. 填入起始位址與大小
4. （可選）點擊「預覽切割內容」確認範圍
5. 點擊「切割並儲存」

### PQC ML-DSA 驗證

1. 切換到「PQC ML-DSA 驗證」頁面
2. 拖曳或選擇目標 bin 檔案
3. 填入各區段偏移量：
   - **明文區塊**：起始位址 + 大小（支援 `0x30000` / `192kb` 格式）
   - **公鑰位置**：起始偏移（大小依演算法自動計算）
   - **簽章位置**：起始偏移（大小依演算法自動計算）
4. 選擇 ML-DSA 演算法版本與 Hash 演算法
5. （可選）勾選「顯示 ML-DSA 內部中間值」以取得完整 debug 資訊
6. 點擊「開始驗證」

---

## 版本紀錄 (Changelog)

### v2.0 (2026-06-10)
- **新增** PQC ML-DSA 簽章驗證工具（第 4 頁）
  - 支援 ML-DSA-44 / ML-DSA-65 / ML-DSA-87（NIST FIPS 204）
  - 大小輸入支援 hex、十進位、kb/mb 後綴
  - 公鑰與簽章大小依演算法自動計算並顯示結束位址
  - Debug 模式：顯示 rho、c~、z、h、tr、mu、c、Az-ct1、w'、c~ match 等完整中間值
  - 直接呼叫 PQClean C 底層函數以確保驗證正確性

### v1.2
- 新增 RSA 簽章 Byte Order 反序功能

### v1.1
- 新增 RSA 簽章解密工具
- 新增 Bin 檔案切割器
- 全面更新 UI 為雙欄佈局

### v1.0
- Hash 計算器初始版本

---

## 授權 (License)

本專案採用 MIT License 授權。

---

## 作者 (Author)

- Yulin Hsieh

#### [喜歡的話歡迎給個打賞吧!](https://ganknow.com/yulin9527/tip)
#### 歡迎用 BTC 支援：  
#### [bc1px0u84lk50jugxzwrcu26h2gsd00ef78pqplrr7sv96qgt8fgnrqqur0x7e](https://blockstream.info/address/bc1px0u84lk50jugxzwrcu26h2gsd00ef78pqplrr7sv96qgt8fgnrqqur0x7e)
#### 掃描 QR Code 進行 BTC 捐贈：
![BTC Donate QR Code](https://api.qrserver.com/v1/create-qr-code/?size=200x200&data=bitcoin:bc1px0u84lk50jugxzwrcu26h2gsd00ef78pqplrr7sv96qgt8fgnrqqur0x7e)


