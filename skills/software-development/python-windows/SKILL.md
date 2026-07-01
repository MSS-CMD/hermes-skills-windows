---
name: python-windows
description: Troubleshoot Python on Windows — locate non-standard installations, fix PATH priority vs WindowsApps aliases, resolve customtkinter compatibility issues on Python 3.14+, and handle encoding problems when editing Chinese/UTF-8 Python files with PowerShell.
platforms:
  - windows
triggers:
  - "Python 没有加到 PATH"
  - "找不到 python"
  - "where python 显示 WindowsApps"
  - "customtkinter tag_config font error"
  - "'font' option forbidden"
  - "PowerShell 修改 Python 文件乱码"
  - "Non-UTF-8 code starting with"
  - "Python 环境变量"
  - "Windows Python 安装"
  - "tia_assistant 运行报错"
  - "tag_config AttributeError"
  - "Python GUI 程序报错"
  - "设置中文 Python 文件编码"
  - "Python 3.14 customtkinter"
---

# Python on Windows — Troubleshooting

## Finding Non-Standard Python Installations

Python on Windows can be installed in several less-common locations:

| Location | How it got there |
|----------|-----------------|
| `%LOCALAPPDATA%\Python\bin\python.exe` | pipx/uv/edgedb-installer style — a self-contained Python distribution with pip bundled in the same dir |
| `%LOCALAPPDATA%\Microsoft\WindowsApps\python.exe` | Windows app execution alias — usually a redirect shim, not a real Python |
| `C:\Program Files\Python3xx\` | Administrator-installed (official installer) |
| `%USERPROFILE%\AppData\Local\Programs\Python\Python3xx\` | User-installed (official installer) |

### Detect installed Pythons

```cmd
where python
py --list
dir %LOCALAPPDATA%\Python\bin\
```

### Verify it works

```cmd
python --version
python -m pip --version
```

## PATH Priority (WindowsApps Aliases)

If `where python` shows `WindowsApps\python.exe` before the real Python, the app execution alias intercepts calls. **Fix: move the real Python path ABOVE WindowsApps in the system/user PATH.**

1. `Win + R` → `sysdm.cpl` → Advanced → Environment Variables
2. Under System (or User) variables, find `Path` → Edit
3. Add `C:\Users\<username>\AppData\Local\Python\bin` (or wherever real Python lives)
4. **Move it to the top** (select → Move Up) so it takes priority over `WindowsApps`
5. OK → open a new cmd to test

## customtkinter Font Error (Python 3.14+)

**Error:**
```
AttributeError: 'font' option forbidden, because would be incompatible with scaling
```

**Cause:** Newer customtkinter (as shipped with Python 3.14+) forbids passing `font=` to `tag_config()` on `CTkTextbox`. Font must be set at widget creation or not at all.

**Fix:** Remove the `font=` parameter from every `tag_config()` call.

```python
# BEFORE (broken)
self.chat_display.tag_config("user", foreground="#8ab4f8", font=ctk.CTkFont(size=12, weight="bold"))

# AFTER (fixed)
self.chat_display.tag_config("user", foreground="#8ab4f8")
```

If you need custom fonts, set a global font when creating the CTkTextbox:

```python
self.chat_display = ctk.CTkTextbox(master, font=ctk.CTkFont(size=12))
```

### Find all offending lines

```cmd
findstr /n "tag_config.*font" main.py
```

## PowerShell Encoding for Chinese/UTF-8 Python Files

**Problem:** `Set-Content main.py` defaults to ANSI encoding on Chinese Windows, corrupting non-ASCII characters and causing `SyntaxError: Non-UTF-8 code starting with` errors.

**Fix — use explicit UTF-8 encoding:**

### Method 1 (PowerShell 5.1+, recommended)
```powershell
[System.IO.File]::WriteAllText((Resolve-Path main.py), $newContent, [System.Text.Encoding]::UTF8)
```

### Method 2 (PowerShell ≥7.0)
```powershell
Set-Content main.py -Encoding UTF8
```

### Method 3 (one-liner replace + save with UTF8)
```powershell
powershell -Command "$c = Get-Content main.py -Raw; $c = $c -replace 'old_pattern', 'new'; [System.IO.File]::WriteAllText((Resolve-Path main.py), $c, [System.Text.Encoding]::UTF8)"
```

### Verify the file is UTF-8
```cmd
python -c "with open('main.py','r',encoding='utf-8') as f: f.read(); print('OK')"
```

## Verification

After any fix, test from a **fresh** cmd:

```cmd
cd C:\path\to\project
python --version
python main.py
```
