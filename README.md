# linux-open-ssh
開啟linux ssh功能&amp;解決連線問題
# 一、安裝 OpenSSH 伺服器

Debian 預設不一定會裝 SSH 服務，先裝上：
```
sudo apt update
sudo apt install -y openssh-server
```

檢查是否正在運作：
```
sudo systemctl status ssh
```

看到類似這樣代表啟動成功 ✅：
```
Active: active (running)
```
# 二、修改 SSH 設定檔

設定檔在：
```
sudo nano /etc/ssh/sshd_config
```

找到以下幾行（有些可能被 # 註解掉，請解除註解並修改成這樣👇）

## 允許 root 登入
```
PermitRootLogin yes
```
## 允許密碼登入
```
PasswordAuthentication yes
```
## 預設埠號（可改成非22增加安全性）
```
Port 22
```
## （可選）限制哪些使用者能登入
```
AllowUsers root
```

儲存離開：

Ctrl + O → Enter → Ctrl + X

# 三、重新啟動 SSH 服務
```
sudo systemctl restart ssh
```

再次確認狀態：
```
sudo systemctl status ssh
```

應該會顯示：
```
Active: active (running)
```
# 四、檢查防火牆 (UFW)

如果你有開啟 UFW 防火牆，要允許 SSH：
```
sudo apt install -y ufw
sudo ufw allow 22/tcp
sudo ufw enable
sudo ufw status
```

如果你改過埠號（例如 Port 2222），請改成：
```
sudo ufw allow 2222/tcp
```
## 五、從 Windows 測試 SSH 登入
## 用 PowerShell：
```
ssh root@192.168.50.124
```

第一次會跳出：

Are you sure you want to continue connecting (yes/no)?

輸入 yes 後，再輸入密碼即可登入。

## 六、（可選）若你要安全性更高
## 禁用密碼登入（改用金鑰登入）

在本地端（Windows）產生金鑰：

ssh-keygen


→ 會在 C:\Users\你的使用者名稱\.ssh\id_rsa.pub 產生公鑰。

然後把它傳到伺服器：

scp "C:\Users\你的使用者名稱\.ssh\id_rsa.pub" root@192.168.50.124:/root/


在伺服器端執行：
```
mkdir -p ~/.ssh
cat ~/id_rsa.pub >> ~/.ssh/authorized_keys
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

最後修改設定檔：
```
sudo nano /etc/ssh/sshd_config
```

改成：
```
PasswordAuthentication no
PermitRootLogin prohibit-password
```

重啟：
```
sudo systemctl restart ssh
```

此時只允許用 SSH 金鑰登入
