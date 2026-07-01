# Python.NET + TIA Portal Openness 连接备忘

## 环境要求
- TIA Portal V18/V19/V20 已安装（含 Automation Openness 组件）
- `pip install pythonnet`（推荐 3.0+）
- Python 和 TIA Portal **不必**以管理员运行（WithGui 模式才需要两者一致）

## 连接步骤

```python
import clr
clr.AddReference("Siemens.Engineering")
from Siemens.Engineering import TiaPortal

# 无参构造 → 后台启动（WithoutGui），无需枚举值
portal = TiaPortal()
# 等待就绪
import time
for _ in range(30):
    try:
        _ = portal.Processes
        break
    except Exception:
        time.sleep(1)
```

## 打开项目

```python
# WithGuaranteedConsistency 的底层整数值是 2
try:
    from Siemens.Engineering import OpenProjectOptions
    opts = OpenProjectOptions.WithGuaranteedConsistency
except ImportError:
    opts = 2  # 整数值回退

portal.OpenProject("C:\\path\\to\\project.ap20", opts)
```

## 已知坑点（V20 PublicAPI）

| 问题 | 现象 | 方案 |
|------|------|------|
| `TiaPortalProcessMode` / `TiaPortalMode` 导入失败 | `cannot import name from 'Siemens.Engineering'` | 无参 `TiaPortal()` 等效 WithoutGui |
| `OpenProjectOptions` 导入失败 | 同上 | 传整数值 `2`（WithGuaranteedConsistency）|
| WithGui 模式连不上 | Projects 为空或空引用 | 权限不一致（UAC）、Openness 未完全安装 |
| `portal.Projects` 为 None | 空引用异常 | 初始化未完成，等 3-5 秒再访问 |
| `TiaPortal` 无 `OpenProject` 属性 | 单参数调用时抛 AttributeError | 必须传两个参数：path + options |

## 常用枚举整数值

| 枚举名 | 整数值 |
|--------|--------|
| `OpenProjectOptions.None` | 0 |
| `OpenProjectOptions.WithUpgrading` | 1 |
| `OpenProjectOptions.WithGuaranteedConsistency` | 2 |
| `OpenProjectOptions.WithoutConsistencyCheck` | 4 |

## 参考链接
- Siemens.Engineering.dll: `C:\Program Files\Siemens\Automation\Portal V20\PublicAPI\V20\`
- TIA Portal Openness 文档: SIOS 门户搜索 "Automation Openness"
