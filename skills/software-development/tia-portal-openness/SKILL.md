---
name: tia-portal-openness
description: Connect to and operate TIA Portal via Siemens.Engineering Openness API from Python using pythonnet — covers connection modes, project operations, PLC block management, compilation, and troubleshooting. Windows only.
platforms:
  - windows
triggers:
  - "TIA Portal Openness"
  - "pythonnet TIA"
  - "Siemens.Engineering"
  - "TiaPortalClient"
  - "Openness 连接"
  - "TIA Portal API"
  - "python clr 西门子"
  - "TIA Portal COM"
  - "TIA Portal 后台启动"
  - "TIA Portal 文件选择"
  - "TIA Portal GUI"
  - "customtkinter TIA"
---

# TIA Portal Openness API

Connect to and program TIA Portal via the Siemens.Engineering .NET assembly using Python.NET (pythonnet).

## Prerequisites

- **TIA Portal V16–V20** installed with the **Automation Openness** component (separate installer option — not installed by default)
- **Python.NET**: `pip install pythonnet`
- **Windows only** — Openness is a Windows .NET Framework assembly (no Linux/Mac support)

## Connection Modes

### WithGui — Attach to running TIA Portal

Connects to an already-running TIA Portal GUI instance.

```python
from Siemens.Engineering import TiaPortal, TiaPortalProcessMode
portal = TiaPortal(TiaPortalProcessMode.WithGui)
```

**Requirements:**
- TIA Portal must be **running with a project open**
- **Both** TIA Portal AND Python process must be **Run as Administrator**
- If UAC levels don't match, COM interop fails silently — `portal.Projects` returns null

**Symptoms when it fails:**
- `TiaPortalProcessMode` imports successfully
- `portal.Processes` succeeds
- `portal.Projects` throws NullReferenceException ("未将对象引用设置到对象的实例")
- The error surfaces as `Siemens.Engineering.TiaPortal.get_Projects()`
- `WithoutGui` mode works fine as fallback

### WithoutGui — Headless background instance

Starts a new headless TIA Portal instance. Always works when Openness is installed.

```python
portal = TiaPortal(TiaPortalProcessMode.WithoutGui)
```

**Notes:**
- Takes 5–10 seconds to initialize
- No project is open by default — call `portal.OpenProject(path)` after attach
- The headless process is visible in Task Manager as `TIA Portal.exe`
- Memory allocation: ~200–400 MB for a V20 headless instance
- **Call `portal.Dispose()` when done** to avoid orphaned processes

## Common Pitfalls

### 1. Openness component not installed
TIA Portal's base installer does NOT include Openness. Re-run the installer → Modify → check **Automation Openness**. If missing, `clr.AddReference("Siemens.Engineering")` might load the DLL but the COM interface won't work.

Verify DLL exists:
```
dir "C:\Program Files\Siemens\Automation\Portal V20\PublicAPI\V20\Siemens.Engineering.dll"
```

### 2. WithGui fails, WithoutGui works (UAC mismatch)
- Run **both** TIA Portal and cmd/PowerShell **as Administrator**
- Or accept WithoutGui as the primary connection mode

### 3. Projects is null after WithGui attach
- Check TIA Portal → Options → Settings → Openness for "Allow remote access..." (varies by version)

### 4. NullReferenceException on portal.Projects
In Python.NET, iterating null .NET objects throws. Always guard:

```python
projects = self.portal.Projects
if projects is None:
    return []
for proj in projects:
    ...
```

### 5. Python 3.14 compatibility
pythonnet 3.1.0 works with Python 3.14.6 (verified 2026). No known Openness issues on this combination.

### 6. Process cleanup (WithoutGui)
WithoutGui leaves orphan `TIA Portal.exe` processes if `Dispose()` is not called.

```python
def detach(self):
    if self.portal is not None:
        self.portal.Dispose()
        self.portal = None
```

### 7. Fallback strategy
When building a resilient Openness client, implement this fallback chain:
1. Try `WithGui` first (attach to existing GUI)
2. If `WithGui` succeeds but `Projects` is null/empty after timeout, fall back to `WithoutGui`
3. In `WithoutGui` mode, auto-open project from config path if no projects found

## Typical Connection Flow

```python
import clr
clr.AddReference("Siemens.Engineering")
from Siemens.Engineering import TiaPortal, TiaPortalProcessMode

portal = TiaPortal(TiaPortalProcessMode.WithoutGui)
time.sleep(5)

for _ in range(30):
    try:
        _ = portal.Processes
        break
    except:
        time.sleep(1)

if not list(portal.Projects):
    portal.OpenProject(r"C:\path\to\project.ap20")

# Use the project
for proj in portal.Projects:
    print(proj.Name)

## GUI Integration (customtkinter)

### customtkinter tag_config font conflict

Newer customtkinter versions forbid `font` in `tag_config()`:
```python
# BAD: 'font' option forbidden
textbox.tag_config("user", foreground="#xxx", font=ctk.CTkFont(size=12))
# GOOD: remove font parameter
textbox.tag_config("user", foreground="#xxx")
```

### File picker for project path

User preference: select project file inside the app, not by editing config.json.

```python
# Right panel widget
path_frame = ctk.CTkFrame(self.right_frame, fg_color="transparent")
path_frame.grid_columnconfigure(0, weight=1)
self.project_path_label = ctk.CTkLabel(
    path_frame, text="📂 未选择项目", font=ctk.CTkFont(size=10), anchor="w",
)
self.project_path_label.grid(row=0, column=0, sticky="ew")
self.browse_btn = ctk.CTkButton(
    path_frame, text="选择项目文件...", height=24,
    font=ctk.CTkFont(size=10), command=self._browse_project_path,
)
self.browse_btn.grid(row=0, column=1, padx=(4, 0))

# Browse handler
def _browse_project_path(self):
    from tkinter import filedialog
    path = filedialog.askopenfilename(
        title="选择 TIA Portal 项目文件",
        filetypes=[("TIA Portal 项目", "*.ap20 *.ap19 *.ap18 *.ap17"), ("所有文件", "*.*")],
        initialdir=os.path.expanduser("~/Documents/Automation/Projects"),
    )
    if path:
        self.config_mgr.set("tia_project_path", path)
        self.project_path_label.configure(text=f"📂 {os.path.basename(path)}")
```

### Auto-open project on connect

```python
def connect(self, project_path="", timeout=30):
    self.client.attach(timeout)
    projects = self.client.get_projects()
    if not projects and project_path:
        self.client.open_project(project_path)
        projects = self.client.get_projects()
```

portal.Dispose()
```

## Project Operations

- `portal.Projects` → list of open projects
- `portal.OpenProject(path)` → open .ap20 project
- `proj.Save()` → save project
- `proj.Close()` → close project
- `proj.Devices` → device tree
- `proj.Name`, `proj.Path` → project metadata

## PLC Operations

Get PLC devices:
```python
for device in project.Devices:
    for item in _walk_devices(device):
        sw = item.GetService("Software")
        if "Plc" in type(sw).Name and "Software" in type(sw).Name:
            plc_software = sw
```

Block operations:
- `plc.BlockGroup.Blocks` → list blocks
- `plc.BlockGroup.Groups` → sub-groups
- `block.Export()` → export to XML string
- `blockGroup.ExternalSources.Import(path)` → import SCL/XML block

Compilation:
```python
comp_svc = plc.GetService("Compile")
result = comp_svc.Compile()
```

See also: `tia-portal-scl` skill for SCL code generation rules.
