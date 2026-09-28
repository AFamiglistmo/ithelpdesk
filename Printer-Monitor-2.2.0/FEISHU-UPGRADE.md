# 飞书通知升级说明

此更新包包含：

- 飞书/Lark 自定义机器人 Webhook 配置界面
- 科技风交互式消息卡片（异常红/橙、恢复绿、测试蓝）
- 卡片展示设备、IP、位置、事件级别和详细说明
- 可选签名密钥
- 测试消息
- 新增告警合并推送
- 恢复通知
- 同类告警级别变化时避免误发“已恢复”通知
- 持久化去重，容器重启不重复推送
- Webhook 域名白名单及服务端脱敏
- 禁止通过静态资源接口读取 JSON 配置
- Docker `CONF=/data/printers.json` 支持
- Aurora ADC240MNA / Brother 私有 OID 粉盒余量解析

## 更新现有 Docker 部署

在服务器上进入部署目录并备份：

```bash
cd /home/clark.li/printr
cp -a Printer-Monitor-main/server.js Printer-Monitor-main/server.js.before-feishu
cp -a Printer-Monitor-main/printer-monitor.html Printer-Monitor-main/printer-monitor.html.before-feishu
```

将更新包解压到源码目录：

```bash
tar -xzf Printer-Monitor-Feishu-update.tar.gz -C Printer-Monitor-main
```

重新构建并启动：

```bash
sudo docker compose build --no-cache
sudo docker compose up -d
sudo docker compose ps
curl -fsS http://127.0.0.1:8899/api/health
```

登录页面后，点击右上角“飞书通知”，填写自定义机器人 Webhook；如果机器人启用了签名校验，同时填写签名密钥。先发送测试消息，成功后勾选启用并保存。

现有 `data/printers.json`、打印机清单和管理员密码不会被覆盖。
