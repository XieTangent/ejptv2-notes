# 🔍 Nikto 使用筆記

---

## 📘 工具簡介

- **Nikto** 是一款開源 Web Server 弱點掃描器。  
- 可偵測過時軟體版本、預設檔案、潛在風險、CGI 漏洞與常見錯誤設定。  
- 適用於滲透測試中 **偵察（Recon）與掃描（Scanning）階段**。  
- 支援 HTTP/HTTPS、多端口、多主機與插件擴充。

---

## ⚙️ 常用指令範例

| 用途             | 指令格式                                                                 |
|------------------|--------------------------------------------------------------------------|
| 基本掃描         | `nikto -h http://target`                                |
| 多端口掃描       | `nikto -h http://target -p 80,8000,8080`                                 |
| 使用 Nmap 結果   | `nmap -p80 192.168.1.0/24 -oG - \| nikto -h -`                            |
| 特定 Plugin 掃描 | `nikto -h http://target -Plugin apacheusers`                             |
| 輸出 HTML 報告   | `nikto -h http://target -o result.html`                                   |
| 顯示重定向資訊   | `nikto -h http://target -Display 1`                                       |

---

## 🧰 常用 flag

| flag / 參數         | 說明                                                             |
|---------------------|------------------------------------------------------------------|
| `-h`                | 指定掃描主機（IP 或 URL）                                       |
| `-p`                | 指定端口（可用逗號分隔）                                        |
| `-Tuning`           | 控制要掃描的漏洞類別（代碼對照如下）                           |
| `-Display`          | 顯示細節（如 1:重定向, 2:Cookies, E:錯誤）                     |
| `-Plugin`           | 指定要使用的插件（如 `apacheusers`）                            |
| `-o`                | 指定輸出檔案（可為 .txt、.html、.csv 等）                      |
| `-f`                | 指定輸出格式（通常自動依副檔名判斷）                            |
| `--list-plugins`    | 顯示所有可用插件                                                 |

---

## 🔌 常見插件 Plugin

| 插件名稱      | 功能說明                                         |
|---------------|--------------------------------------------------|
| `apacheusers` | 嘗試列舉 Apache 認證帳號                         |
| `cgi`         | 尋找可利用的 CGI 腳本                            |
| `robots`      | 分析 `robots.txt`，找出網站限制訪問的路徑         |
| `dir_traversal` | 嘗試目錄遍歷（Local File Inclusion, LFI）       |

> 📌 查看全部插件：
> `nikto --list-plugins`

---

## 🧪 Tuning 類型代碼（漏洞類型）

| 類型編號 | 描述                                       |
|----------|--------------------------------------------|
| `0`      | 檔案上傳功能                               |
| `2`      | 錯誤設定 / 預設敏感檔案                     |
| `3`      | 資訊洩露（如版本號、HTTP Header）           |
| `4`      | 注入漏洞（XSS、HTML 注入等）                |
| `8`      | 系統命令執行漏洞                           |
| `9`      | SQL Injection 檢測                         |

---

## 📝 輸出掃描報告

- 使用 `-o` 指定輸出檔名，副檔名自動決定格式：
  - `.txt` 純文字
  - `.html` 網頁格式
  - `.csv` 可匯入 Excel

範例：

```markdown
nikto -h http://target -o result.html
```

---
