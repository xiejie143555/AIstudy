# Claude Code 项目级别 MCP 服务器安装教程

## 案例背景

在本案例中，用户在项目根目录下配置了 `.mcp.json` 文件，但 MCP 服务器没有生效。经过排查发现，Claude Code 的项目级别 MCP 配置需要额外的配置文件才能正确识别和启用。

## 安装步骤

### 第一步：创建 MCP 服务器定义文件

在项目根目录创建 `.mcp.json` 文件：

```json
{
  "mcpServers": {
    "your-server-name": {
      "type": "stdio",
      "command": "npx",
      "args": [
        "-y",
        "your-mcp-package",
        "additional-args"
      ],
      "env": {
        "API_KEY": "your-api-key"
      }
    }
  }
}
```

### 第二步：创建项目级 Claude 配置文件

创建 `.claude/settings.json` 文件（需要创建 `.claude` 目录）：

```json
{
  "enableAllProjectMcpServers": true,
  "enabledMcpjsonServers": ["your-server-name"]
}
```

### 第三步：验证配置

运行以下命令检查 MCP 服务器是否生效：

```bash
claude mcp list
```

预期输出：
```
Checking MCP server health...
your-server-name: command - ✓ Connected
```

## 踩过的坑（重点注意事项）

### 1. ❌ 以为 `.mcp.json` 会自动生效
**错误认知**：在项目根目录放 `.mcp.json` 文件就会自动被识别
**实际情况**：Claude Code 不会自动加载项目级别的 `.mcp.json`，需要额外的配置文件

### 2. ❌ 使用 `claude mcp add` 命令添加项目级 MCP
**错误操作**：
```bash
claude mcp add --transport stdio server-name command
```
**问题**：这个命令默认添加到用户级别配置，而不是项目级别

### 3. ❌ 命令行参数解析错误
**错误尝试**：
```bash
claude --mcp-config .mcp.json mcp list
```
**问题**：Claude Code 的参数解析机制不支持这种方式

### 4. ❌ 缺少项目配置文件
**错误**：只创建 `.mcp.json`，没有创建 `.claude/settings.json`
**结果**：MCP 服务器不会被启用

## 正确的项目级与用户级区别

### 项目级别配置
- **文件位置**：项目根目录的 `.mcp.json` + `.claude/settings.json`
- **作用范围**：仅当前项目
- **版本控制**：可以提交到 Git，团队共享
- **优先级**：高于用户级别配置

### 用户级别配置
- **文件位置**：`~/.claude.json` 或 `~/.claude/settings.json`
- **作用范围**：所有项目
- **版本控制**：不应提交到 Git
- **优先级**：低于项目级别配置

## 文件结构参考

```
your-project/
├── .mcp.json                    # MCP 服务器定义
├── .claude/
│   └── settings.json           # 启用项目级 MCP 配置
├── .gitignore                  # 确保 .claude 目录被版本控制
└── ...                         # 其他项目文件
```

## .gitignore 建议

确保项目级 MCP 配置能被团队共享：

```
# 允许共享项目 MCP 配置
!.mcp.json
!.claude/
!.claude/settings.json

# 但排除用户特定配置
.claude/local.json
```

## 验证命令

### 检查 MCP 服务器状态
```bash
claude mcp list
```

### 检查项目配置
```bash
# 查看当前项目配置
claude mcp list --scope project

# 查看用户配置
claude mcp list --scope user
```

### 删除错误的配置
```bash
# 如果添加了用户级配置，需要删除
claude mcp remove "server-name" -s local
```

## 最佳实践

1. **始终使用项目级别配置**：确保 MCP 服务器配置与项目代码一起版本控制
2. **创建完整的配置文件**：不要只创建 `.mcp.json`，还要创建 `.claude/settings.json`
3. **验证配置生效**：每次配置后都运行 `claude mcp list` 验证
4. **避免用户级配置**：除非确实需要全局使用的 MCP 服务器
5. **文档化配置**：在 README 中说明项目需要的 MCP 配置

## 常见错误排查

### MCP 服务器未显示
- ✅ 检查 `.mcp.json` 语法是否正确
- ✅ 确认 `.claude/settings.json` 存在且配置正确
- ✅ 验证 MCP 服务器名称在 `enabledMcpjsonServers` 列表中

### 连接失败
- ✅ 检查网络连接
- ✅ 验证 API 密钥是否有效
- ✅ 确认 MCP 包已安装（`npx` 命令可用）

### 权限问题
- ✅ 确保有执行命令的权限
- ✅ 检查环境变量是否正确设置

通过遵循这个教程，你可以正确安装和配置项目级别的 MCP 服务器，避免常见的配置错误。