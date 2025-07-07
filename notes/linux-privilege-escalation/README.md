# 🛡️ Linux Privilege Escalation 筆記整理

本筆記整理涵蓋了 Linux 權限提升的各種常見手法，搭配 TryHackMe 等平台學習內容，方便日後複習與查閱。

## 📁 筆記總覽

| 檔案名稱 | 說明 |
|----------|------|
| [Enumeration](enumeration.md) | 權限提升前的資訊蒐集技巧，例如檔案、使用者、SUID、計劃任務等。 |
| [Privileg Sudo and SUDI](privileg-sudo-sudi.md) | 利用 `Sudo` 或 `SUDI` 權限執行指令提權，包括 GTFOBins 的應用。 |
| [Privileg Path](privileg-path.md) | `PATH` 變數操控與執行順序劫持實現提權。 |
| [Privileg Cron Jobs](privileg-cron-jobs.md) | 利用系統排程任務（Cron Jobs）進行提權的技巧。 |
| [Privileg NFS](privileg-nfs.md) | 利用 NFS（Network File System）設定不當進行提權。 |
| [Privilege Capabilities](privilege-capabilities.md) | Linux capabilities 權限機制簡介與提權範例。 |

## 🧰 常使用的指令

| 指令 | 說明 |
|-------|------|
| `sudo -l` | 檢查目前使用者可以透過 `sudo` 執行的指令，是提權過程的重要步驟。即使無法直接 `sudo /bin/bash`，也可能發現可被濫用的程式。 |
| `find / -type f -perm -04000 -ls 2>/dev/null` | 搜尋所有具有 SUID 權限的可執行檔，常見於提權漏洞掃描。 |
| `getcap -r / 2>/dev/null` | 搜尋系統中具有特殊 Linux Capabilities 權限的檔案，這些也可能被濫用來提權。 |
| `cat /etc/crontab` | 檢查系統定時任務（Cron Jobs），不當設定可能允許低權限用戶寫入並提權。 |
| `cat /etc/exports` | 查看 NFS 匯出設定，若有 `no_root_squash` 可能被用來提權。 |
| `find / -writable 2>/dev/null` | 尋找可寫目錄，有助於尋找可放置惡意程式或替換檔案的機會。可搭配 `cut -d "/" -f 2,3 \| sort -u` 使用
| `export PATH=/tmp:$PATH` | 修改 `PATH` 環境變數，常用於 `PATH` 劫持提權攻擊（尤其是配合錯誤使用相對路徑的 `sudo` 指令）。 |

## 🧰 常使用的資源
| 名稱 | 說明 |
|----------|------|
| [GTFOBins](https://gtfobins.github.io/) | 收錄各種 Linux 程式的提權技巧，包括 `sudo`, `SUID`, `LD_PRELOAD`、`PATH` 濫用等情境。非常適合搭配 `sudo -l` 後進行提權分析。 |