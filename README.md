# Hermes Skills - TIA Portal (博图)

Siemens TIA Portal 博图自动化技能包。

## 安装方法

```bash
git clone https://github.com/MSS-CMD/hermes-skills-windows.git
cp -r hermes-skills-windows/skills/* ~/.hermes/skills/
# 然后在 Hermes 中运行 /reload-skills
```

## 技能

| 技能 | 说明 |
|------|------|
| `tia-portal-scl` | 生成 TIA Portal SCL（结构化控制语言）代码，含 FB/FC 调用、IEC 定时器、边沿检测、数组/循环、状态机、类型安全、错误处理 |
| `tia-portal-openness` | TIA Portal Openness COM 自动化，Python + pythonnet 操控 TIA，自动生成/编译/下载 PLC 程序 |
