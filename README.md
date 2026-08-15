# firewall-panel

基于 Flask 的轻量级 Windows 防火墙 IP 黑名单管理服务。  
提供 Web 管理界面，支持一键添加/删除封锁 IP，自动同步至系统防火墙（`netsh advfirewall`），并具备开机自启、端口放行、防火墙状态监测等实用功能。

> **说明**：本项目原为内部使用工具（曾用名 CoreNet-Diag），现已开源。代码中部分规则前缀已更新为 FirewallPanel。

## 系统要求

- **Windows 10 及以上**（Windows 7 未测试，不保证兼容）
- 需要管理员权限运行

## 功能特性

- 📋 **Web 管理界面**：可视化查看、添加、删除封锁 IP 列表
- 🔒 **防火墙自动同步**：修改配置后自动更新 Windows 防火墙入站/出站规则
- 🚀 **开机自启管理**：一键设置或取消程序随系统启动（写入注册表 Run 键）
- 🛡️ **防火墙状态检测**：实时检测防火墙是否开启，并提供一键修复
- 🔌 **自放行端口**：自动放行 Web 服务所使用的端口，避免自身被拦截
- 🔄 **配置热加载**：监听 `config.json` 文件变化，无需重启服务即可同步规则
- 🔐 **HTTP 基础认证**：管理页面和 API 受用户名/密码保护（默认 admin/123456）

## 快速开始

请以**管理员身份**运行终端。

```bash
# 1. 克隆项目
git clone https://github.com/jiu-zui-90817/firewall-panel.git
cd firewall-panel

# 2. 安装依赖
pip install -r requirements.txt
# 或者只装 Flask
pip install flask

# 3. 直接运行（开发调试）
python app.py

# 4. （可选）打包为独立 exe
pip install pyinstaller
pyinstaller --onefile --add-data "templates;templates" app.py
# 生成的 exe 位于 dist/app.exe
```

## 配置说明

首次运行会自动生成 `config.json`（与可执行文件同目录）：

```json
{
  "web_port": 51883,
  "admin_user": "admin",
  "admin_pass": "123456",
  "blocked_ips": []
}
```

修改 `blocked_ips` 会自动同步到防火墙规则。修改端口或账号密码后需要重启服务。

## API 接口

所有接口均需 HTTP Basic Auth（默认 admin/123456）。

| 端点 | 方法 | 说明 |
|------|------|------|
| `/` | GET | 管理首页 |
| `/api/status` | GET | 获取防火墙/端口/开机自启状态 |
| `/api/startup` | POST | 设置开机自启 `{"action":"enable"}` 或 `"disable"` |
| `/api/block` | POST | 添加封锁 IP `{"ip":"1.2.3.4"}` |
| `/api/unblock` | POST | 移除封锁 IP `{"ip":"1.2.3.4"}` |
| `/api/repair_firewall` | POST | 强制开启防火墙并重新同步规则 |

## 注意事项

1. **必须管理员权限**：否则无法修改防火墙规则。
2. 程序创建的规则前缀为 `FirewallPanel-Block-`，方便识别和管理。
3. 打包后的 exe 请尽量避免放在含中文或特殊字符的路径下。
4. 本工具仅供合法网络管理使用，请勿用于非法用途。

## 许可证

[MIT](LICENSE)

## 贡献

欢迎提交 Issue 或 Pull Request。
