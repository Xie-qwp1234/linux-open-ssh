# linux-open-ssh
開啟 linux ssh 功能 &amp; 解決連線問題
## 一、安裝 OpenSSH 伺服器

Debian 預設不一定會裝 SSH 服務，先裝上：
```bash
sudo apt update
sudo apt install -y openssh-server
```

檢查是否正在運作：
```bash
sudo systemctl status ssh
```

看到類似這樣代表啟動成功 ✅：
```console
Active: active (running)
```
## 二、修改 SSH 設定檔

設定檔在：
```bash
sudo nano /etc/ssh/sshd_config
```

找到以下幾行（有些可能被 # 註解掉，請解除註解並修改成這樣👇）

### 允許 root 登入
```bash
PermitRootLogin yes
```
### 允許密碼登入
```bash
PasswordAuthentication yes
```
### 預設埠號（可改成非 22 增加安全性）
```bash
Port 22
```
### （可選）限制哪些使用者能登入
```bash
AllowUsers root
```

儲存離開：

<kbd>Ctrl</kbd> <kbd>O</kbd> → <kbd>Enter</kbd> → <kbd>Ctrl</kbd> <kbd>X</kbd>

## 三、重新啟動 SSH 服務
```bash
sudo systemctl restart ssh
```

再次確認狀態：
```bash
sudo systemctl status ssh
```

應該會顯示：
```console
Active: active (running)
```
## 四、檢查防火牆 (UFW)

如果你有開啟 UFW 防火牆，要允許 SSH：
```bash
sudo apt install -y ufw
sudo ufw allow 22/tcp
sudo ufw enable
sudo ufw status
```

如果你改過埠號（例如 Port 2222），請改成：
```bash
sudo ufw allow 2222/tcp
```
## 五、從 Windows 測試 SSH 登入
### 用 PowerShell：
```bash
ssh root@192.168.50.124
```

第一次會跳出：
```console
Are you sure you want to continue connecting (yes/no)?
```
輸入 `yes` 後，再輸入密碼即可登入。

## 六、（可選）若你要安全性更高
### 禁用密碼登入（改用金鑰登入）

在本地端（Windows）產生金鑰：
```console
ssh-keygen
```

→ 會在 `C:\Users\你的使用者名稱\.ssh\id_rsa.pub` 產生公鑰。

然後把它傳到伺服器：
```bash
scp "C:\Users\你的使用者名稱\.ssh\id_rsa.pub" root@192.168.50.124:/root/
```

在伺服器端執行：
```bash
mkdir -p ~/.ssh
cat ~/id_rsa.pub >> ~/.ssh/authorized_keys
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

最後修改設定檔：
```bash
sudo nano /etc/ssh/sshd_config
```

改成：
```bash
PasswordAuthentication no
PermitRootLogin prohibit-password
```

重啟：
```bash
sudo systemctl restart ssh
```

此時只允許用 SSH 金鑰登入
