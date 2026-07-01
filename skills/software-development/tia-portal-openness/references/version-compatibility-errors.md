# TIA Portal Openness 版本兼容错误实录

## 错误 1: TiaPortalProcessMode 不存在

**报错信息：**
```
cannot import name 'TiaPortalProcessMode' from 'Siemens.Engineering' (unknown location)
```

**场景：** Python 3.14.6，TIA Portal V18+，`pythonnet` 已安装且 `clr.AddReference("Siemens.Engineering")` 成功。

**根因：** TIA Portal V18+ 的 Siemens.Engineering 程序集已移除 `TiaPortalProcessMode` 枚举。旧版（V17 及以下）需要传这个参数。

**修复：** try/except fallback

```python
from Siemens.Engineering import TiaPortal

try:
    from Siemens.Engineering import TiaPortalProcessMode
    portal = TiaPortal(TiaPortalProcessMode.WithGui)
except ImportError:
    portal = TiaPortal()
```

## 错误 2: customtkinter tag_config font forbidden

**报错信息：**
```
AttributeError: 'font' option forbidden, because would be incompatible with scaling
```

**场景：** customtkinter 新版 + Python 3.14，`CTkTextbox.tag_config()` 传了 `font=` 参数。

**根因：** 新版 customtkinter 禁止在 tag_config 里设置 font，因为会影响缩放。

**修复：** 删掉所有 `tag_config()` 中的 `font=ctk.CTkFont(...)` 参数。改用 CTkTextbox 构造时的 `font=` 指定全局字体。

```python
# 构造时设全局字体
self.chat_display = ctk.CTkTextbox(master, font=ctk.CTkFont(size=12))

# tag_config 不再传 font
self.chat_display.tag_config("user", foreground="#8ab4f8")
self.chat_display.tag_config("assistant", foreground="#a8d8a8")
```

## 注意

PowerShell 的 `Set-Content` 默认编码非 UTF-8，会破坏带中文的 Python 文件。编辑含中文的 .py 文件时，必须用 `[System.IO.File]::WriteAllText(path, content, [System.Text.Encoding]::UTF8)` 或直接用 `python -c` 修改。
