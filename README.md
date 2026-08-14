# CoreNet-Diag 防火墙管理工具

基于 Flask 的轻量级 Windows 防火墙 IP 黑名单管理服务。提供 Web 管理界面，支持一键添加/删除封锁 IP，自动同步至系统防火墙（`netsh advfirewall`），并具备开机自启、端口放行、防火墙状态监测等实用功能。

## 功能特性

- 📋 **Web 管理界面**：可视化查看、添加、删除封锁 IP 列表
- 🔒 **防火墙自动同步**：修改配置后自动更新 Windows 防火墙入站/出站规则
- 🚀 **开机自启管理**：一键设置或取消程序随系统启动（写入注册表 Run 键）
- 🛡️ **防火墙状态检测**：实时检测防火墙是否开启，并提供一键修复
- 🔌 **自放行端口**：自动放行 Web 服务所使用的端口，避免自身被拦截
- 🔄 **配置热加载**：监听 `config.json` 文件变化，无需重启服务即可同步规则
- 🔐 **HTTP 基础认证**：管理页面和 API 受用户名/密码保护（默认 admin/123456）

## 快速开始（一键复制所有命令）

以下命令涵盖了 **克隆、安装依赖、运行服务、打包 exe** 等所有操作，按顺序执行即可（请以管理员身份运行终端）。

```bash
# 1. 克隆项目（或直接下载源码）
git clone https://github.com/yourusername/CoreNet-Diag.git
cd CoreNet-Diag

# 2. 安装 Python 依赖（仅 Flask）
pip install flask

# 3. 直接运行（开发调试）
python app.py

# 4. （可选）打包为独立 exe，无需 Python 环境
pip install pyinstaller
pyinstaller --onefile --add-data "templates;templates" app.py
# 生成的 exe 位于 dist/app.exe，可双击运行
```

> **注意**：所有防火墙操作均需管理员权限，请以管理员身份运行命令或 exe。

## 配置说明

首次运行会自动生成 `config.json`，位于可执行文件同目录（源码模式位于项目根目录）。配置项如下：

```json
{
  "web_port": 51883,          // Web 服务端口
  "admin_user": "admin",      // 管理员用户名
  "admin_pass": "123456",     // 管理员密码
  "blocked_ips": []           // 初始封锁 IP 列表
}
```

修改 `blocked_ips` 数组即可增删 IP，服务会**自动同步**到防火墙规则。  
修改端口或凭证后需重启服务生效。

## API 接口

所有接口均需 HTTP Basic Auth 认证（默认 admin/123456）。

| 端点 | 方法 | 说明 |
|------|------|------|
| `/` | GET | 管理首页（HTML） |
| `/api/status` | GET | 获取防火墙状态、自放行状态、开机自启状态 |
| `/api/startup` | POST | 设置开机自启，`{"action":"enable"}` 或 `"disable"` |
| `/api/block` | POST | 添加 IP 到黑名单，`{"ip":"1.2.3.4"}` |
| `/api/unblock` | POST | 从黑名单移除 IP，`{"ip":"1.2.3.4"}` |
| `/api/repair_firewall` | POST | 强制开启防火墙、放行本机端口并重新同步所有规则 |

## 注意事项

1. **管理员权限**：防火墙规则修改需要管理员权限，建议以管理员身份运行程序。
2. **防火墙默认开启**：若系统防火墙完全关闭，程序会在启动时尝试开启（仅公共、专用、域三个配置文件均启用）。
3. **规则命名**：程序创建的防火墙规则均以 `CoreNet-Diag-Block-` 为前缀，方便识别和管理。
4. **并发安全**：配置同步采用后台线程轮询文件修改时间，避免频繁读写。
5. **中文路径**：若打包为 exe，请勿将文件放在包含中文或特殊字符的路径下，以免 PowerShell 命令解析异常。

## 常见问题

**Q：为什么添加 IP 后防火墙规则未生效？**  
A：请检查防火墙是否已开启，并确认程序拥有管理员权限。可访问 `/api/repair_firewall` 强制修复。

**Q：如何修改默认端口？**  
A：编辑 `config.json` 中的 `web_port`，重启服务即可。程序会自动放行新端口。

**Q：开机自启无效？**  
A：确保程序路径不含空格或特殊字符，必要时手动将 exe 快捷方式放入 `启动` 文件夹。

## 许可证

[MIT](LICENSE)

## 贡献

欢迎提交 Issue 或 Pull Request。

---
*本工具仅供内部网络管理使用，请勿用于非法用途。*