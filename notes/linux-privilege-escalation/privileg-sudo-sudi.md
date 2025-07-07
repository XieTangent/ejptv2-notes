# 🛡 Linux 權限提升基本概念、攻擊條件與攻擊流程

## 基本概念

- **sudo**：允許指定使用者以 root 身份執行特定命令。
- **SUID 程式**：執行時會以檔案擁有者（通常是 root）身份運行，若配置不當，可能成為提權跳板。
- **攻擊重點在於：**
  - 利用 sudo 誤設指令來取得 shell 或修改系統檔案，例如 `sudo nmap`, `sudo less`。
  - 濫用 SUID 程式執行不安全動作，如可執行 shell 的程式（如 vim、python、perl）。
  - 控制環境變數（如 `LD_PRELOAD`、`PATH`）讓 SUID 或 sudo 程式載入攻擊者的程式碼。
  - 搭配 GTFOBins 查詢可利用的程式及指令範例。

---

## 🎯 攻擊條件

- 目標使用者擁有 sudo 權限，但限制不嚴，允許執行可濫用程式。
- 系統存在擁有 SUID 權限的程式，且該程式有可被利用的功能（如執行 shell、編輯檔案、載入環境變數等）。

---

## 攻擊步驟

### 1. 查詢關鍵資訊

- 確認可使用的 sudo 命令與權限。
  ```bash
  sudo -l
  ```

- 找出系統中 SUID 程式。
  ```bash
  find / -perm -4000 -type f 2>/dev/null
  ```

### 2. 查詢漏洞
- 透過 [GTFOBins](https://gtfobins.github.io/) 可查詢相關漏洞

---

## 小結

- 權限提升依賴目標系統的錯誤配置或漏洞。
- 必須先偵查 sudo 權限與系統 SUID 程式。
- 攻擊重點為濫用 sudo 命令或利用 SUID 程式執行 root 權限的任意命令。
- 了解各種 Linux 權限控制與利用方式是攻擊成功的關鍵。

