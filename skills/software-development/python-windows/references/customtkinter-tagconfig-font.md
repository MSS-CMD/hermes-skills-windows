# customtkinter tag_config 'font' option forbidden

## Error

```
File "main.py", line 404, in _append_chat
    self.chat_display.tag_config("user", foreground="#8ab4f8", font=ctk.CTkFont(size=12, weight="bold"))
    ...\customtkinter\windows\widgets\ctk_textbox.py", line 449, in tag_config
    raise AttributeError("'font' option forbidden, because would be incompatible with scaling")
AttributeError: 'font' option forbidden, because would be incompatible with scaling
```

## Environment

- Python 3.14.6 (checked via `py --list`)
- customtkinter (version bundled with Python 3.14 Core)
- Windows 11

## Root Cause

Newer customtkinter (as shipped with Python 3.14's bundled packages) actively forbids passing `font=` to `tag_config()` on `CTkTextbox`. Tag-level font styling is incompatible with the widget's DPI-aware scaling system.

## Fix

Remove the `font=` parameter from all `tag_config()` calls. The font must be set globally at widget creation time or omitted entirely.

### Before (4 lines in this case)
```python
self.chat_display.tag_config("user", foreground="#8ab4f8", font=ctk.CTkFont(size=12, weight="bold"))
self.chat_display.tag_config("assistant", foreground="#a8d8a8", font=ctk.CTkFont(size=12, weight="bold"))
self.chat_display.tag_config("system", foreground="#f0c040", font=ctk.CTkFont(size=12, weight="bold"))
self.chat_display.tag_config("text", font=ctk.CTkFont(size=13))
```

### After
```python
self.chat_display.tag_config("user", foreground="#8ab4f8")
self.chat_display.tag_config("assistant", foreground="#a8d8a8")
self.chat_display.tag_config("system", foreground="#f0c040")
self.chat_display.tag_config("text")
```

### Find all affected lines
```cmd
findstr /n "tag_config.*font" main.py
```

## Notes

- The `font` at `CTkTextbox(master, font=...)` creation level IS still supported.
- This applies only to `tag_config()` calls, not `CTkTextbox` constructor.
- If you need distinct fonts per tag, the only workaround is to use separate `CTkTextbox` widgets or set font at widget level and accept uniform styling.
- Caused by commit that introduced DPI-aware scaling enforcement in customtkinter's text widget tag system.
