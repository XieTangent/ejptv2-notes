# 🛡️ eJPT v2 筆記庫

這是我為準備 eLearnSecurity Junior Penetration Tester (eJPT v2) 考試所整理的學習筆記，涵蓋網路基礎、滲透測試技巧、工具使用教學與實戰經驗。

---

## 🐧 Linux 系統與安全

| 主題 | 簡介 |
|------|------|
| [Linux 指令與權限](./notes/linux.md) | 常用 Linux 指令、檔案系統與權限管理 |
| [Linux Privilege Escalation](./notes/linux-privilege-escalation/README.md) | 權限提升技巧與常見攻擊方式（如 SUID、sudo、capabilities） |

---

## 🛠️ 常用工具

| 工具名 | 簡介 |
|--------|------|
| [Nmap](./notes/tools/nmap.md)         | 強大的網路掃描工具，主要用於端口掃描、服務偵測與系統指紋辨識 |
| [Burp Suite](./notes/tools/burpsuite.md) | Web 應用安全測試平台，提供代理攔截、漏洞掃描與漏洞利用輔助工具 |
| [Hydra](./notes/tools/hydra.md) | 密碼爆破工具，支援多種協議如 SSH、HTTP、FTP 等 |
| [John](./notes/tools/john.md) | 密碼破解工具，常用於破解 hash 值 |
| [Hashcat](./notes/tools/hashcat.md) | hash 破解工具|
| [Gobuster](./notes/tools/gobuster.md) | 網站目錄、子網域、虛擬主機枚舉工具，支援多模式掃描 |
| [Nikto](./notes/tools/nikto.md) | 網站安全掃描工具，能檢測常見漏洞與錯誤配置 |
| [Wpscan](./notes/tools/wpscan.md) | 專門針對 WordPress 平台的安全掃描工具，用於發現漏洞和弱點 |

---

## 🌐 常用資源

| 資源名稱 | 簡介 |
|----------|------|
| [GTFOBins](https://gtfobins.github.io/) | Linux 下可被濫用以提權的指令查詢庫（搭配 sudo/SUID 使用） |
| [Exploit-DB](https://www.exploit-db.com/) | 漏洞利用程式資料庫，收錄已公開漏洞的 PoC 與 Exploit 程式碼 |

---

## 🏆 CTF 筆記

| 主題 | 簡介 |
|------|------|
| [Easy Peasy](./notes/challenges/easy_peasy/README.md) | 涵蓋端口掃描、Web 掃描、hash 破解、資料隱寫及 Linux 權限提升 |

---