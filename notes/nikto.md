# Nikto 使用筆記

---

## 基本掃描 (Basic Scanning)

- 使用 `-h` 並提供 IP 或網域名稱執行基本掃描。
- 掃描會回傳 Web Server 或應用程式的標頭（如 Apache2、Tomcat、Jenkins、JBoss）。
- 會尋找敏感檔案或目錄（例如 login.php、/admin/ 等）。

範例：  
nikto -h vulnerable_ip

---

## 掃描多主機與多端口 (Scanning Multiple Hosts & Ports)

- 可結合 Nmap 掃描結果，掃描整個網段或多台主機。
- 使用 Nmap 的 `-oG` 輸出格式讓 Nikto 可讀取。

範例：  
nmap -p80 172.16.0.0/24 -oG - | nikto -h -

- 掃描單一主機多個端口：  
nikto -h 10.10.10.1 -p 80,8000,8080

---

## Plugins 外掛功能

- 使用掃描結果，選擇適合的插件強化掃描能力。
- 列出所有插件：  
nikto --list-plugins

### 常用插件

| 插件名稱       | 功能說明                                   |
|----------------|--------------------------------------------|
| apacheusers    | 嘗試列舉 Apache HTTP 認證用戶             |
| cgi            | 尋找可利用的 CGI 腳本                      |
| robots         | 分析 robots.txt 文件限制訪問的路徑          |
| dir_traversal  | 嘗試目錄遍歷攻擊 (LFI)，尋找系統敏感檔案     |

### 使用插件範例

nikto -h 10.10.10.1 -Plugin apacheusers

---

## Verbose 模式 (顯示更詳細掃描資訊)

- 使用 `-Display` 參數增加輸出資訊。

| 參數值 | 功能說明               | 使用理由                               |
|--------|------------------------|--------------------------------------|
| 1      | 顯示 Web 伺服器的重定向 | 可能導向特定檔案或目錄，調整掃描方向    |
| 2      | 顯示收到的 Cookies      | 了解應用的 Session 或存取資料機制        |
| E      | 輸出錯誤訊息           | 偵錯掃描異常時使用                      |

---

## Tuning 掃描類別 (Tuning Your Scan)

- 使用 `-Tuning` 指定要掃描的漏洞類型。

| 類別名稱         | 描述                                       | Tuning 參數 |
|------------------|------------------------------------------|-------------|
| File Upload      | 搜尋允許上傳檔案的功能                     | 0           |
| Misconfigurations / Default Files | 搜尋預設敏感檔案或錯誤設定               | 2           |
| Information Disclosure | 蒐集伺服器資訊，如版本號、HTTP 標頭          | 3           |
| Injection       | 搜尋可進行注入攻擊的地方（XSS、HTML 等）     | 4           |
| Command Execution | 搜尋允許執行系統命令的漏洞                   | 8           |
| SQL Injection   | 搜尋 URL 參數中可能的 SQL 注入漏洞            | 9           |

---

## 輸出掃描報告 (Saving Your Findings)

- 使用 `-o` 指定輸出檔案名稱，可用文字或 HTML 格式。
- 使用 `-f` 指定輸出格式，但 Nikto 會自動根據副檔名判斷。

範例：  
nikto -h http://ip_address -o report.html
