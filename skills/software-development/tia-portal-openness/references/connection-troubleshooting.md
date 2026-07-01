# TIA Portal Openness 连接故障排查记录

## 场景：Python 3.14 + TIA Portal V20 + customtkinter GUI

### 错误 1: `TiaPortalProcessMode` 导入失败
```
cannot import name 'TiaPortalProcessMode' from 'Siemens.Engineering' (unknown location)
```
**原因**：部分 TIA Portal 版本（V18 某些补丁/V16/V17）的 Siemens.Engineering 程序集中不包含 `TiaPortalProcessMode` 枚举。

**修复**：用 try/except 包裹，兼容两种构造方式：
```python
try:
    from Siemens.Engineering import TiaPortalProcessMode
    portal = TiaPortal(TiaPortalProcessMode.WithGui)
except ImportError:
    portal = TiaPortal()  # 无参构造
```

### 错误 2: `TiaPortal.Projects` 返回 null
```
未将对象引用设置到对象的实例。
   在 Siemens.Engineering.TiaPortal.get_Projects()
```
**原因**：`TiaPortal(TiaPortalProcessMode.WithGui)` 构造成功但未成功附加到运行中的 GUI 实例。常见原因：
- Python 脚本未以管理员身份运行
- TIA Portal 未以管理员身份运行
- Openness 组件未安装完整
- 多用户会话问题

**修复分两步**：
1. 先试 WithGui，等 2 秒后检测 Projects
2. 如果 Projects 为空，清理后降级到 WithoutGui（后台启动新实例），等 5 秒再检测

**判空保护**：迭代 Projects 前必须判 None，否则 pythonnet 抛异常。

### 错误 3: customtkinter + Python 3.14 tag_config font 报错
```
AttributeError: 'font' option forbidden, because would be incompatible with scaling
```
**原因**：customtkinter 新版本（随 Python 3.14 打包的版本）禁止在 `tag_config()` 中传 `font` 参数，必须通过 `CTkTextbox` 全局 font 设置。

**修复**：删掉所有 `tag_config` 调用中的 `font=ctk.CTkFont(...)` 参数。

### 最终连接状态
WithGui 模式在非管理员运行下无法获取 Projects，但 WithoutGui 模式成功启动后台 TIA Portal 实例并连接。提示"✅ 已连接 TIA Portal（无打开项目）"说明连接成功但无项目加载。
