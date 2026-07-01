# Hermes Skills for Windows

Windows 专属 Hermes Agent 技能包，共 **3 个技能**。

## 安装方法

```bash
git clone https://github.com/MSS-CMD/hermes-skills-windows.git
cp -r hermes-skills-windows/skills/* ~/.hermes/skills/
# 然后在 Hermes 中运行 /reload-skills
```

## 技能清单

| 技能 | 说明 |
|------|------|
| `hermes-agent` | **必装** — Hermes 完整命令参考、配置说明、**Windows 专属注意事项**（Alt+Enter 换行、UTF-8 BOM、WinError 10106 等） |
| `hermes-web-ui` | Web 管理面板（端口 8648），含密码重置、登录解锁、更新等操作指引 |
| `python-windows` | Windows Python 环境配置（customtkinter、tag_config 等踩坑记录） |
