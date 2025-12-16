# NIST Debug 瑞士刀 (NIST Debug Swiss Army Knife)

[![Python Version](https://img.shields.io/badge/python-3.8%2B-blue.svg)](https://www.python.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

一個為開發者、韌體工程師與資安研究員設計的桌面小工具，旨在簡化處理 BIOS/UEFI 韌體或其他二進位檔案時的常見任務。它整合了三大核心功能：**Hash 計算器**、**RSA 簽章解密**與 **Bin 檔案切割器**。

---

## 功能特色 (Features)

本工具提供三大模組，可透過頂部選單隨時切換。

### 1. 🧮 Hash 計算器 (Hash Calculator)

一個強大且靈活的雜湊值計算工具。

*   **多種演算法**：支援 `SHA-256`, `SHA-384`, `SHA-512`, `MD5`, `CRC32`。
*   **區域計算**：可計算整個檔案，或指定**起始位址 (Offset)** 與**大小 (Size)** 來計算檔案的特定區塊。
*   **直覺操作**：支援拖曳檔案，操作簡單方便。

!Hash 計算器介面
*(圖片：Hash 計算器介面)*

### 2. 🔐 RSA 簽章解密 (RSA Signature Decryption)

專為解密 RSA 簽章以還原原始訊息（通常是 Hash）而設計。在分析韌體驗證流程時特別有用。

*   **支援多種公鑰格式**：
    *   標準 `PEM` 格式公鑰。
    *   二進位 (`.bin`) 格式的公鑰模數 (Modulus)。
*   **處理不同金鑰長度**：支援 `RSA-2048` 與 `RSA-4096`。
*   **關鍵的位元組順序 (Byte Order) 支援**：
    *   支援標準 Big-endian。
    *   支援 **Little-endian (反序)**。這對於處理某些 BIOS/UEFI 韌體中儲存的公鑰至關重要，因為它們常以反序方式儲存。
*   **智慧解析**：
    *   自動偵測並嘗試移除 `PKCS#1 v1.5` padding。
    *   自動從 ASN.1 `DigestInfo` 結構中提取雜湊值。

!RSA 簽章解密介面
*(圖片：RSA 簽章解密介面)*

### 3. ✂️ Bin 檔案切割器 (Binary File Splitter)

一個簡單的二進位檔案切割工具，用於從大檔案中提取特定區塊。

*   **精準切割**：透過指定**起始位址**和**大小**來提取資料。
*   **即時預覽**：在儲存前可預覽切割內容 (Hex 格式)，確保選取範圍正確。
*   **自動命名**：若不指定檔名，會根據來源檔案、起始位址和大小自動產生建議檔名。

!Bin 檔案切割器介面
*(圖片：Bin 檔案切割器介面)*

---

## 下載與使用 (Download & Usage)

1.  前往本專案的 **Releases 頁面**。
2.  下載最新版本的 `NistDebugTool.exe` 執行檔。
3.  下載後無需安裝，直接點兩下執行即可。

程式啟動後，您可以透過頂部的「工具」選單切換不同的功能頁面。

---

## 操作說明 (Instructions)

### Hash 計算器
1.  將檔案拖曳到視窗中，或點擊「瀏覽」按鈕選擇檔案。
3.  (可選) 在「讀取設定」中輸入「起始位址」和「大小」。位址和大小支援十進位 (如 `12288`) 或十六進位 (如 `0x3000`)。若「大小」留空，則會讀取至檔案結尾。
4.  選擇要使用的 Hash 演算法。
5.  點擊「開始計算 Hash」，結果將顯示在右側。

### RSA 簽章解密
1.  切換到「RSA 簽章解密」頁面。
2.  分別拖曳或瀏覽選擇「RSA 簽章檔案」與「RSA 公鑰檔案」。
3.  選擇正確的「RSA 金鑰長度」（2048 或 4096）。
4.  **如果您的公鑰是 `.bin` 格式**，請選擇正確的「公鑰 Byte Order」。
    *   **提示**：許多 UEFI 韌體中的公鑰是以 Little-endian 格式儲存的，請優先嘗試選擇 **"Little-endian (反序)"**。
5.  點擊「解密簽章」。工具將在右側顯示詳細的解密過程與結果，包含 Padding 和 DigestInfo 的解析狀態，並盡力提取最終的 Hash 值。

### Bin 檔案切割器
1.  切換到「切割 Bin 檔案」頁面。
2.  拖曳或瀏覽選擇要切割的 Bin 檔案。
3.  在「切割設定」中輸入「起始位址」和「大小」。
4.  (可選) 點擊「預覽切割內容」按鈕，在右側視窗檢查 Hex 內容。
5.  點擊「切割並儲存」，程式會提示您選擇儲存位置與檔名。

---

## 授權 (License)

本專案採用 MIT License 授權。

---

## 作者 (Author)

*   Yulin Hsieh

#### [喜歡的話歡迎給個打賞吧!](https://ganknow.com/yulin9527/tip)
#### 歡迎用 BTC 支援：  
#### [bc1px0u84lk50jugxzwrcu26h2gsd00ef78pqplrr7sv96qgt8fgnrqqur0x7e](https://blockstream.info/address/bc1px0u84lk50jugxzwrcu26h2gsd00ef78pqplrr7sv96qgt8fgnrqqur0x7e)
#### 掃描 QR Code 進行 BTC 捐贈：
![BTC Donate QR Code](https://api.qrserver.com/v1/create-qr-code/?size=200x200&data=bitcoin:bc1px0u84lk50jugxzwrcu26h2gsd00ef78pqplrr7sv96qgt8fgnrqqur0x7e)


