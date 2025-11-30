# 设置Claude别名（跨平台完整解决方案）

## 🎯 目标
在Windows电脑上实现跨终端（PowerShell、CMD、Git Bash）的`cc`别名，输入`cc`等同于`claude --dangerously-skip-permissions`。

## ⚠️ 重要提醒：路径示例说明

**本文档中的所有路径都是示例路径，请根据您的实际系统环境进行相应调整！**

### 🔍 如何找到您的实际路径

#### 查找claude安装路径：
```bash
# 在Git Bash中运行
which claude
# 可能返回：/c/Users/您的用户名/.local/bin/claude

# 或者在CMD中运行
where claude
# 可能返回：C:\Users\您的用户名\.local\bin\claude.exe

# 或者在PowerShell中运行
Get-Command claude | Select-Object -ExpandProperty Source
# 可能返回：C:\Users\您的用户名\.local\bin\claude.exe
```

#### 查找您的用户目录：
```bash
# Windows用户目录通常是：
# C:\Users\您的用户名\
# 其中"您的用户名"是您在Windows系统中的登录用户名
```

### 📋 路径替换示例表

| 文档中的示例路径 | 您需要替换为的实际路径 |
|-----------------|---------------------|
| `C:\Users\Administrator\` | `C:\Users\您的用户名\` |
| `Administrator` | 您的Windows用户名 |
| `/c/Users/Administrator/` | `/c/Users/您的用户名/` |

## 🚧 踩过的坑（重点提醒）

### ❌ PowerShell 执行策略陷阱
**问题**：PowerShell默认禁止执行脚本，会报`无法加载文件...因为在此系统上禁止运行脚本`错误。
**解决**：避免使用需要执行策略的配置文件，改用函数方式。

### ❌ 批处理文件路径陷阱
**问题**：PowerShell对`.bat`文件的处理与CMD不同，即使文件在PATH中也可能找不到。
**解决**：PowerShell使用函数而不是依赖批处理文件。

### ❌ 系统目录权限陷阱
**问题**：试图在`C:\Windows`目录创建文件需要管理员权限，会报`EPERM: operation not permitted`错误。
**解决**：使用用户目录下的`bin`文件夹。

## 🔧 详细实现步骤

### 1. 基础准备
```bash
# 检查claude安装路径（重要：请使用您的实际路径）
which claude
# 应该返回类似：/c/Users/您的用户名/.local/bin/claude

# 创建用户bin目录（如果不存在）
mkdir -p /c/Users/您的用户名/bin
```

### 2. Git Bash 配置
```bash
# 编辑 ~/.bashrc 文件
echo 'alias cc="claude --dangerously-skip-permissions"' >> ~/.bashrc

# 立即生效
source ~/.bashrc

# 验证
alias cc
```

### 3. CMD 配置
创建批处理文件：
```batch
# 文件路径：C:\Users\您的用户名\bin\cc.bat
# 注意：请根据您的实际claude路径修改下面这行
@echo off
"C:\Users\您的用户名\.local\bin\claude.exe" --dangerously-skip-permissions %*
```

**关键**：确保`C:\Users\您的用户名\bin`在系统PATH中。通常Windows会自动包含这个目录。

### 4. PowerShell 配置（重点）
**不要**使用需要执行策略的配置文件！改用函数方式：

创建函数配置文件：
```powershell
# 文件路径：C:\Users\您的用户名\Documents\WindowsPowerShell\profile.ps1
# 注意：请根据您的实际claude路径修改下面这行
# Claude Code 快捷命令
function global:cc {
    & "C:\Users\您的用户名\.local\bin\claude.exe" --dangerously-skip-permissions @args
}
```

**注意**：使用`profile.ps1`而不是`Microsoft.PowerShell_profile.ps1`，避免执行策略问题。

### 5. 验证配置

#### Git Bash 验证：
```bash
cc --version
# 应该显示版本号
```

#### CMD 验证：
```cmd
cc --version
# 应该显示版本号
```

#### PowerShell 验证：
```powershell
# 检查是否能找到命令
Get-Command cc

# 测试执行
cc --version
# 应该显示版本号
```

## 📁 文件结构总结（通用模板）

```
C:\Users\[您的用户名]\
├── .bashrc                                    # Git Bash 别名配置
├── bin\
│   └── cc.bat                                # CMD 批处理文件
└── Documents\
    └── WindowsPowerShell\
        └── profile.ps1                       # PowerShell 函数配置
```

## 🔍 故障排除

### PowerShell 问题
如果仍然无法识别`cc`命令：
1. 检查函数是否加载：`Get-Command cc`
2. 检查路径是否正确：`Test-Path 'C:\Users\您的用户名\.local\bin\claude.exe'`
3. 重启PowerShell会话

### CMD 问题
如果CMD无法识别：
1. 检查PATH：`echo %PATH%`
2. 确认文件存在：`dir C:\Users\您的用户名\bin\cc.bat`
3. 尝试完整路径运行：`C:\Users\您的用户名\bin\cc.bat --version`

### Git Bash 问题
如果Git Bash无法识别：
1. 检查配置文件：`cat ~/.bashrc`
2. 重新加载：`source ~/.bashrc`
3. 检查claude路径：`which claude`

## 🎯 最终验证

在任意终端中输入：
```bash
cc --version
```

应该都能显示：
```
2.x.x (Claude Code)
```

## 💡 最佳实践

1. **统一路径**：所有配置都使用相同的claude路径
2. **避免管理员权限**：不要尝试修改系统目录
3. **函数优于别名**：PowerShell使用函数而不是别名
4. **逐步验证**：每步配置后都要验证，避免一次性配置过多
5. **路径检查**：始终使用您的实际用户名和claude安装路径

## 🚀 使用方法

配置完成后，在任何终端中输入：
```bash
cc
```
等同于：
```bash
claude --dangerously-skip-permissions
```

现在您可以在PowerShell、CMD和Git Bash中无缝使用`cc`命令了！

---

## 📖 附录：如何查找您的系统信息

### 查找Windows用户名：
```cmd
# 在CMD中运行
echo %USERNAME%

# 或者在PowerShell中运行
$env:USERNAME
```

### 查找claude所有可能的位置：
```powershell
# 在PowerShell中运行，查找claude.exe
Get-ChildItem -Path $env:USERPROFILE -Recurse -Name "claude.exe" -ErrorAction SilentlyContinue
```

### 检查PATH环境变量：
```cmd
# 在CMD中运行
echo %PATH%

# 或者在PowerShell中运行
$env:PATH -split ';'
```