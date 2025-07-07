# 🛠 Linux 權限提升技巧：Capabilities（功能）

## 📘 概念介紹

在 Linux 中，**Capabilities** 是一種將 root 權限「細分」的方法。  
傳統上只有 root 才能做某些事（例如開 raw socket、mount 檔案系統），而 Capabilities 允許單一可執行檔獲得這些權限，而不用整個程式變成 root（像 SUID 那樣）。
- **攻擊重點在於：**
  - 某些程式若被賦予高權限 Capabilities（如 `cap_setuid`, `cap_setgid`, `cap_sys_admin`），就可能被濫用以提權。
  - 攻擊者可利用具 `cap_setuid+ep` 的二進位執行檔建立 SUID 效果（變成 root 身份執行）。
  - 若使用者有權限修改帶有 Capabilities 的程式內容（如擁有寫入權限），可將其替換為惡意程式。
  - 系統中存在的程式若同時具備可執行權限與危險 Capabilities，可成為提權跳板。
  - 搭配 `getcap -r / 2>/dev/null` 可掃描整個系統，找出具有 Capabilities 的可執行檔。


## 🎯 攻擊條件

1. 可執行檔被授權特定 Capability。例如：`CAP_SETUID`、`CAP_NET_RAW`、`CAP_SYS_PTRACE` 等
2. 攻擊者發現該程式可以執行特權行為
3. 如果該程式可控（如為自己寫的腳本、或可被注入環境變數等），可利用該 Capability 來取得更高權限或 root shell。

### 常見濫用 Capability

| Capability | 說明 | 利用方式 |
|------------|------|----------|
| `CAP_SETUID` | 允許變更 UID | 利用此權限將 UID 設為 0（root） |
| `CAP_SYS_PTRACE` | 允許追蹤其他進程 | 用來注入 shellcode 至其他 root 程序 |
| `CAP_NET_RAW` | 允許建立 raw socket | 可偽造封包或執行 ping scan |
| `CAP_DAC_READ_SEARCH` | 可略過檔案權限限制 | 可讀取原本無權限的檔案內容 |

---

## 攻擊步驟

### 1. 查詢關鍵資訊
- 查詢整個系統中哪些檔案被賦予 Capability。
  ```bash
  getcap -r / 2>/dev/null
  ```
  
### 2. 查詢漏洞
- 透過 [GTFOBins](https://gtfobins.github.io/) 可查詢相關漏洞
---

## 🛡 防禦建議

- 使用 `getcap -r /` 定期掃描系統
- 移除非必要的 Capability 設定
- 審查第三方或自訂程式是否有可被濫用的功能

---

## 📌 小結

- Capabilities 是 Linux 上更細緻的權限控管機制
- 攻擊者可利用被授權的二進位檔進行提權
- 是 Linux 權限提升中常見、但容易被忽略的一環
