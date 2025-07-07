# 🕵️‍♂️ Linux Enumeration 筆記

Linux 權限提升的第一步是枚舉（Enumeration）。本篇整理你在登入低權限帳號後應立即執行的系統資訊偵察指令，幫助你發現潛在的提權路徑。

---

## 📘 目錄（Table of Contents）
- [🔧 基本系統資訊](#🔧-基本系統資訊)
- [👤 使用者與身份資訊](#👤-使用者與身份資訊)
- [📂 檔案與目錄檢查](#📂-檔案與目錄檢查)
- [🔍 程式與行程偵察](#🔍-程式與行程偵察)
- [🌐 網路與連線狀況](#🌐-網路與連線狀況)
- [🌿 環境變數偵察](#🌿-環境變數偵察)
- [🧰 常見 Linux 權限提升自動化工具](#🧰-常見-Linux-權限提升自動化工具)


---

## 🔧 基本系統資訊

| 指令 | 說明 |
|------|------|
| `hostname` | 查看系統主機名，可能透露角色（如 `SQL-PROD-01`） |
| `uname -a` | 顯示完整的核心版本與系統資訊 |
| `cat /proc/version` | 顯示 kernel version 與是否裝有 GCC 編譯器 |
| `cat /etc/issue` | 查看系統版本資訊（可自訂，非絕對） |

---

## 👤 使用者與身份資訊

| 指令 | 說明 |
|------|------|
| `id` | 顯示目前使用者的 UID、GID 與所屬群組 |
| `cat /etc/passwd` | 查看系統所有使用者（可搭配 `| cut -d ':' -f` 篩出使用者） |

| `cat /etc/shadow` | 查看系統所有使用者密碼（可搭配 `| cut -d ':' -f` 篩出使用者） |
| `history` | 檢視過去執行的指令，可能含密碼或重要操作紀錄 |

---

## 📂 檔案與目錄檢查

| 指令 | 說明 |
|------|------|
| `ls -la` | 一定要加 `-la` 才不會漏掉隱藏檔 |
| `find / -perm -u=s -type f 2>/dev/null` | 尋找 SUID bit 的檔案（提權常用） |
| `find / -writable -type d 2>/dev/null` | 尋找可寫入的資料夾 (可搭配cut -d "/" -f 2 | sort -u)|
| `find / -name *.sh` | 搜尋腳本檔案 |
| `find / -type f -size +100M 2>/dev/null` | 找出可疑的大型檔案 |
| `find / -name perl*`、`python*`、`gcc*` | 確認系統有無支援開發工具與語言 |

---

## 🔍 程式與行程偵察

| 指令 | 說明 |
|------|------|
| `ps` | 顯示目前 shell 的行程 |
| `ps -A`、`ps aux` | 顯示所有行程 |
| `ps axjf` | 以樹狀顯示行程，觀察關聯性與父子程序 |
| `sudo -l` | 檢查當前帳號可執行的 sudo 指令（可能可提權） |

---

## 🌐 網路與連線狀況

| 指令 | 說明 |
|------|------|
| `ifconfig` | 顯示網卡與 IP，偵察是否可 pivot 到其他網段 |
| `ip route` | 查看路由資訊 |
| `netstat -ano` | 所有 socket，IP、port、PID |
| `netstat -lntup` | 所有 TCP/UDP 的 listening port |
| `netstat -tp` | 顯示服務名稱與對應 PID |
| `netstat -i` | 顯示介面流量資訊 |
| `netstat -s` | 顯示使用各協定的統計資料 |

---

## 🌿 環境變數偵察

| 指令 | 說明 |
|------|------|
| `env` | 查看環境變數，觀察 PATH、SHELL、編譯器等路徑設定 |

---

## 🧰 常見 Linux 權限提升自動化工具

這些工具可大幅加快系統枚舉與提權向量搜尋的速度，但請注意：它們**可能無法涵蓋所有情境**，使用後仍應手動檢查可能的提權方式。

| 工具名稱 | 說明 | GitHub 連結 |
|----------|------|--------------|
| LinPEAS | 最知名的提權偵察腳本，涵蓋 SUID、cron、環境變數、憑證等 | [LinPEAS 🔗](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/linPEAS) |
| LinEnum | 老牌工具，列出許多系統資訊與提權可能性 | [LinEnum 🔗](https://github.com/rebootuser/LinEnum) |
| LES (Linux Exploit Suggester) | 依據 kernel 版本建議已知漏洞與提權 Exploit | [LES 🔗](https://github.com/mzet-/linux-exploit-suggester) |
| Linux Smart Enumeration | 輕量、條列式輸出，適合不支援 ncurses 的系統 | [LSE 🔗](https://github.com/diego-treitos/linux-smart-enumeration) |
| Linux Priv Checker | Python 腳本，輸出包含 sudo、SUID、cron、PATH 等 | [Priv Checker 🔗](https://github.com/linted/linuxprivchecker) |

📌 提醒：
- 若目標機沒有 Python，你將無法執行 Python 工具
- 建議多熟一點工具，依現場環境選擇使用

---

