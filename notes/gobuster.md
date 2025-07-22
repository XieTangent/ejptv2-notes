# 🔍 Gobuster 完整筆記

---

## 📘 工具簡介

- Gobuster 是一款用 Golang 開發的開源暴力破解工具。  
- 主要用途為 **枚舉網站目錄、子網域、虛擬主機、雲端儲存資源等**。  
- 在滲透測試中屬於 **偵察（Recon）與掃描（Scanning）階段之間** 的關鍵工具。  
- 運作方式：透過字典，針對目標不斷嘗試並解析伺服器回應。

---


## ⚙️ 一般常用 flag

| flag     | long flag               | 說明                                        |
|----------|-------------------------|---------------------------------------------|
| -w       | --wordlist              | 指定要使用的字典檔案（必要）               |
| -t       | --threads               | 執行緒數（預設為 10，可調高加快掃描）      |
| -o       | --output                | 將結果輸出到指定檔案                        |
| --delay  | --delay                 | 請求間延遲時間，避免被偵測或封鎖             |
| --debug  | --debug                 | 顯示除錯資訊，有助於排錯                      |

---

### 🧰 系統內建字典

| 字典路徑                                                            | 說明                            |
|---------------------------------------------------------------------|---------------------------------|
| `/usr/share/wordlists/dirbuster/directory-list-2.3-small.txt`      | DirBuster 小型字典（較快）      |
| `/usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt`     | DirBuster 中型字典（平衡）      |
| `/usr/share/wordlists/dirbuster/directory-list-1.0.txt`            | DirBuster 舊版字典               |
| `/usr/share/wordlists/dirb/small.txt`                              | dirb 小型字典                    |
| `/usr/share/wordlists/dirb/common.txt`                             | dirb 常用字典（常見目錄）       |
| `/usr/share/wordlists/dirb/big.txt`                                | dirb 大型字典（全面但較慢）     |
| `/usr/share/wordlists/dirb/extensions_common.txt`                  | 常見副檔名列表，適合檔案 fuzzing |

## 可透過 [SecLists](https://github.com/danielmiessler/SecLists) 獲得更多字典

---

#### 通過命令查看更多
```bash
gobuster --help
```
---

## 📁 dir 模式 — 網頁目錄與檔案枚舉

### 用途

- 枚舉網站中未列出的目錄與檔案（如 `/admin/`、`/login.php`）。  
- 發現隱藏或敏感頁面，如備份檔、設定檔、測試路徑等。

### 常用參數

| flag           | long flag            | 說明                                                                                       |
|----------------|----------------------|--------------------------------------------------------------------------------------------|
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


#### 通過命令查看更多
```bash
gobuster dir --help
```

---

### 使用範例
```bash
gobuster dir -u http://example.thm -w /usr/share/wordlists/dirb/common.txt -t 50
```

---

## 🌐 dns 模式 — 子網域列舉

### 用途

- 暴力破解列舉子網域，例如：  
  `dev.example.thm`、`admin.example.thm`、`test.example.thm`。  
- 有些漏洞只存在於特定子域，列舉是偵察關鍵。

### 常用參數

| flag         | long flag       | 說明                                         |
|--------------|-----------------|----------------------------------------------|
| -c           | --show-cname    | 顯示 CNAME 記錄（不能與 -i 同時使用）。      |
| -i           | --show-ips      | 顯示域和子域解析到的 IP 位址。               |
| -r           | --resolver      | 配置自訂的 DNS 伺服器用於解析。              |
| -d           | --domain        | 指定要枚舉的目標網域。                       |

#### 通過命令查看更多
```bash
gobuster dns --help
```

---

### 使用範例

```bash
gobuster dns -d example.thm -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt -i
```

- 可搭配 SecLists 字典進行大量測試。  
- 使用 `-i` 顯示 IP，便於進行下一步掃描。  
- 若要繞過內網限制，可用 `-r 8.8.8.8` 指定 DNS 伺服器。

---

## 🏷 vhost 模式 — 虛擬主機列舉

### 用途

- 枚舉同一 IP 上是否存在多個虛擬主機（虛擬網站）。  
- 一台伺服器可同時託管多個網頁，攻擊者可針對不同 vhost 尋找漏洞。

### 常用參數

| flag             | long flag         | 說明                                                                                   |
|------------------|-------------------|----------------------------------------------------------------------------------------|
| -u               | --url             | 指定強制虛擬主機名稱的基本 URL（目標網域）。                                         |
| --append-domain   | --append-domain   | 將基本網域附加到字典中每個單字（如 word.example.com）。                              |
| -m               | --method          | 指定用於請求的 HTTP 方法（例如 GET、POST）。                                          |
| --domain         | --domain          | 將網域附加到字典中每個條目形成有效主機名稱，當未明確提供時非常有用。                  |
| --exclude-length | --exclude-length  | 根據回應主體長度排除結果，有助於過濾不需要的回應。                                   |
| -r               | --follow-redirect | 遵循 HTTP 重定向，適用於子網域可能重定向的情況。                                     |


#### 通過命令查看更多
```bash
gobuster vhost --help
```

### 使用範例

```bash
gobuster vhost -u http://10.10.10.10 -w /usr/share/wordlists/SecLists/Discovery/DNS/namelist.txt
```

- 若伺服器對 Host 標頭有反應，就會顯示該虛擬主機存在。  
- 可用於偵測未公開的測試網站、備份站等。

---

## 🧠 小結

- Gobuster 是滲透測試中的關鍵列舉工具，操作快速且穩定。  
- **dir 模式** 掃網站目錄，**dns 模式** 掃子網域，**vhost 模式** 掃虛擬主機。  
- 合理調整執行緒數與請求延遲，搭配合適的字典，可顯著提高發現率。  
- 可搭配其他工具如 `dig`、`wfuzz`、`ffuf`、`nmap` 進行更完整的偵察與後續分析。
