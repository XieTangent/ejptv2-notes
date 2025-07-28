# 🔍 Gobuster 完整筆記

---

## 📘 工具簡介

- Gobuster 是一款用 Golang 開發的開源暴力破解工具。  
- 主要用途為 **枚舉網站目錄、子網域、虛擬主機、雲端儲存資源等**。  
- 在滲透測試中屬於 **偵察（Recon）與掃描（Scanning）階段之間** 的關鍵工具。  
- 運作方式：透過字典，針對目標不斷嘗試並解析伺服器回應。

---

## ⚙️ 常用指令

| 模式       | 說明                                                                 | 範例指令 |
|------------|----------------------------------------------------------------------|----------|
| `dir` 模式 | - 枚舉網站中未列出的目錄與檔案（如 `/admin/`、`/login.php`）<br>- 發現隱藏頁面、備份檔、測試資料夾等 | `gobuster dir -u http://example.thm -w /usr/share/wordlists/dirb/common.txt` |
| `dns` 模式 | - 暴力破解列舉子網域，如 `dev.example.thm`、`admin.example.thm`<br>- 用於發現特定子網域上的漏洞 | `gobuster dns -d example.thm -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt -i` |
| `vhost` 模式 | - 列舉一個 IP 上可能存在的虛擬主機（多網站共用同一 IP）<br>- 可針對不同 vhost 尋找漏洞與弱點 | `gobuster vhost -u http://10.10.10.10 -w /usr/share/wordlists/SecLists/Discovery/DNS/namelist.txt` |


---

## 🌐 常用資源

| 資源名稱/路徑                                   | 說明                                  |
|------------------------------------------------|-------------------------------------|
| [SecLists](https://github.com/danielmiessler/SecLists) | 豐富且經常更新的字典資源集合         |
| `/usr/share/wordlists/dirbuster/directory-list-2.3-small.txt`      | DirBuster 小型字典（較快）      |
| `/usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt`     | DirBuster 中型字典（平衡）      |
| `/usr/share/wordlists/dirbuster/directory-list-1.0.txt`            | DirBuster 舊版字典               |
| `/usr/share/wordlists/dirb/small.txt`                              | dirb 小型字典                    |
| `/usr/share/wordlists/dirb/common.txt`                             | dirb 常用字典（常見目錄）       |
| `/usr/share/wordlists/dirb/big.txt`                                | dirb 大型字典（全面但較慢）     |
| `/usr/share/wordlists/dirb/extensions_common.txt`                  | 常見副檔名列表，適合檔案 fuzzing |

---

## ⚙️ 通用 flag

| flag          | long flag                | 說明                                         |
|---------------|--------------------------|----------------------------------------------|
| -w            | --wordlist               | 指定要使用的字典檔案（必要）                |
| -t            | --threads                | 執行緒數（預設為 10，可調高加快掃描）       |
| -o            | --output                 | 將結果輸出到指定檔案                         |
| --delay       | --delay                  | 請求間延遲時間，避免被偵測或封鎖            |
| --debug       | --debug                  | 顯示除錯資訊，有助於排錯                      |

## ⚙️ DIR flag

| flag          | long flag                | 說明                                         |
|---------------|--------------------------|----------------------------------------------|
| -c             | --cookies            | 配置一個 Cookie 傳遞每個請求，例如會話 ID。                                               |
| -x             | --extensions         | 指定要掃描的檔案副檔名，例如 .php、.js。                                                  |
| -H             | --headers            | 配置每個請求要傳遞的完整標頭（如 Authorization）。                                        |
| -k             | --no-tls-validation  | 使用 HTTPS 時跳過憑證驗證，常見於使用自簽名證書的 CTF 或考場。                           |
| -n             | --no-status          | 不顯示收到的每個回應的狀態碼，讓輸出更清晰。                                            |
| -P             | --password           | 配合 --username 使用，執行需驗證的請求。                                                 |
| -s             | --status-codes       | 設定顯示的 HTTP 狀態碼範圍，例如 200 或 300-400。                                        |
| -b             | --status-codes-blacklist | 配置不想顯示的狀態碼，會覆蓋 -s 標誌。                                                 |
| -U             | --username           | 配合 --password 使用，執行需驗證的請求。                                                 |
| -r             | --followredirect     | 追蹤 HTTP 重定向，例如 301 或 302 狀態碼，跟隨轉向 URL。                                  |

## ⚙️ DNS flag

| flag         | long flag       | 說明                                         |
|--------------|-----------------|----------------------------------------------|
| -c           | --show-cname    | 顯示 CNAME 記錄（不能與 -i 同時使用）。      |
| -i           | --show-ips      | 顯示域和子域解析到的 IP 位址。               |
| -r           | --resolver      | 配置自訂的 DNS 伺服器用於解析。              |
| -d           | --domain        | 指定要枚舉的目標網域。                       |

## ⚙️ VHOST flag

| flag             | long flag         | 說明                                                                                   |
|------------------|-------------------|----------------------------------------------------------------------------------------|
| -u               | --url             | 指定強制虛擬主機名稱的基本 URL（目標網域）。                                         |
| --append-domain   | --append-domain   | 將基本網域附加到字典中每個單字（如 word.example.com）。                              |
| -m               | --method          | 指定用於請求的 HTTP 方法（例如 GET、POST）。                                          |
| --domain         | --domain          | 將網域附加到字典中每個條目形成有效主機名稱，當未明確提供時非常有用。                  |
| --exclude-length | --exclude-length  | 根據回應主體長度排除結果，有助於過濾不需要的回應。                                   |
| -r               | --follow-redirect | 遵循 HTTP 重定向，適用於子網域可能重定向的情況。                                     |