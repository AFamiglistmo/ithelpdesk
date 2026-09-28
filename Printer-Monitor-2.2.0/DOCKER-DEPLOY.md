# Docker 全新部署

此发行包不含打印机清单、管理员密码、飞书 Webhook 或其他运行配置。首次部署会在 `data` 目录生成新配置。

## 1. 解压并进入目录

```bash
tar -xzf Printer-Monitor-2.2.0-full.tar.gz
cd Printer-Monitor-2.2.0
```

## 2. 准备持久化目录

容器使用 UID/GID `1000:1000` 运行：

```bash
sudo install -d -o 1000 -g 1000 -m 750 data
```

## 3. 构建并启动

```bash
sudo docker compose build --pull
sudo docker compose up -d
sudo docker compose ps
curl -fsS http://127.0.0.1:8899/api/health
```

浏览器访问 `http://服务器IP:8899`。

## 4. 设置指定的 admin 密码

密码至少 8 位，不限制字符组成。以下方式不会把密码显示在终端或写进 shell 历史：

```bash
read -rsp '请输入新的 admin 密码: ' PM_NEW_PASSWORD
echo
read -rsp '请再次输入新密码: ' PM_NEW_PASSWORD_CONFIRM
echo
if [ "$PM_NEW_PASSWORD" != "$PM_NEW_PASSWORD_CONFIRM" ]; then
  echo '两次密码不一致，请重新操作'
else
  sudo docker exec printer-monitor node /app/set-password.js admin "$PM_NEW_PASSWORD"
  sudo docker restart printer-monitor
fi
unset PM_NEW_PASSWORD PM_NEW_PASSWORD_CONFIRM
```

密码设置成功后，从 `http://服务器IP:8899/login` 登录。

## 5. 配置飞书通知

登录面板，点击右上角“飞书通知”，填写飞书自定义机器人 Webhook。如果机器人启用了签名校验，同时填写签名密钥。先点“发送测试”，确认收到消息后勾选启用并保存。

## 常用命令

```bash
sudo docker compose logs -f --tail=100
sudo docker compose restart
sudo docker compose down
sudo docker compose up -d --build
```

运行数据只保存在宿主机的 `data/`。迁移为全新实例时，不要复制该目录。
