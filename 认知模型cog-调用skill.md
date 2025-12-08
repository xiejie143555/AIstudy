---
name: project-cog
description: Cognitive model defining core entities and relationships
---

# 日子 - 认知模型 (Cog)

<meta>
  <document-id>day-cog</document-id>
  <version>1.0.0</version>
  <project>日子</project>
  <type>Cognitive Model</type>
  <created>2024-12-08</created>
  <depends>real.md</depends>
</meta>

## Document Purpose

基于"智能体 + 信息 + 上下文"框架，定义"日子"日记应用的核心实体、关系和信息流动模式。

---

## 1. Agents

<agents>

### 1.1 Human Agents

<agent type="human" id="A1">
<name>用户（记录者）</name>
<identifier>用户ID（UUID）或邮箱地址</identifier>
<classification>
  <by-usage-frequency>日常记录者 | 偶尔记录者 | 深度使用者</by-usage-frequency>
  <by-content-type>生活记录 | 学习笔记 | 工作思考 | 情感表达</by-content-type>
</classification>
<capabilities>创建日记、查看历史、导出数据、授权AI分身</capabilities>
<goals>记录生活、整理思绪、自我反思、留下回忆</goals>
</agent>

### 1.2 AI Agents

<agent type="ai" id="A2">
<name>AI理解引擎</name>
<identifier>Vercel AI SDK + 指定模型（如gpt-4）</identifier>
<classification>
  <by-function>内容理解 | 情感分析 | 主题提取 | 时间线整理</by-function>
</classification>
<interaction-pattern>接收用户文本输入 → 分析处理 → 生成理解结果或建议</interaction-pattern>
</agent>

<agent type="ai" id="A3">
<name>AI数字分身</name>
<identifier>基于用户历史数据训练的个性化模型</identifier>
<classification>
  <by-authorization-status>已授权 | 未授权</by-authorization-status>
  <by-training-progress>训练中 | 可用 | 暂停</by-training-progress>
</classification>
<interaction-pattern>在获得授权后，基于用户历史记录生成个性化回应</interaction-pattern>
</agent>

</agents>

---

## 2. Information

<information>

### 2.1 Core Entities

<entity id="E1">
<name>日记记录</name>
<unique-code>日期编码（YYYYMMDD）+ 用户ID + 序号（如有多条）</unique-code>
<classification>
  <by-content-type>生活日记 | 学习笔记 | 工作记录 | 情感抒发 | 灵感想法</by-content-type>
  <by-privacy-level>私密 | 可分享（目前仅支持私密）</by-privacy-level>
  <by-length>短文（<500字） | 长文（≥500字）</by-length>
</classification>
<attributes>标题、内容、创建时间、修改时间、标签、心情、天气、位置（可选）</attributes>
<relations>属于一个用户；可包含多个标签；可有多个心情标记</relations>
</entity>

<entity id="E2">
<name>打卡记录</name>
<unique-code>日期编码（YYYYMMDD）+ 用户ID + 打卡类型编码</unique-code>
<classification>
  <by-activity-type>阅读 | 运动 | 学习 | 早起 | 早睡 | 冥想 | 其他自定义</by-activity-type>
  <by-frequency>每日打卡 | 每周打卡 | 自定义周期</by-frequency>
</classification>
<attributes>打卡类型、完成状态、完成时间、备注、连续天数</attributes>
<relations>属于一个用户；关联特定的打卡计划</relations>
</entity>

<entity id="E3">
<name>日历</name>
<unique-code>用户ID + 年月编码（YYYYMM）</unique-code>
<classification>
  <by-view-type>月视图 | 周视图 | 年视图</by-view-type>
  <by-content-density>稀疏 | 中等 | 密集</by-content-density>
</classification>
<attributes>所属用户、时间范围、视图类型、显示设置</attributes>
<relations>关联多个日记记录；关联多个打卡记录</relations>
</entity>

<entity id="E4">
<name>AI理解结果</name>
<unique-code>关联记录ID + "_ai" + 时间戳</unique-code>
<classification>
  <by-analysis-type>情感分析 | 主题提取 | 关键词 | 时间线事件</by-analysis-type>
  <by-confidence-level>高置信度 | 中等置信度 | 低置信度</by-confidence-level>
</classification>
<attributes>分析类型、结果内容、置信度、创建时间、模型版本</attributes>
<relations>关联到具体的日记记录或打卡记录</relations>
</entity>

### 2.2 Information Flow

<information-flow>
<flow id="F1" name="日记创建流程">
  用户 → 打开应用 → 点击新建 → 输入内容 → 保存 → 系统加密存储 → 返回确认 → 用户
</flow>
<flow id="F2" name="AI分析流程">
  用户保存记录 → 触发AI分析 → AI引擎处理内容 → 生成理解结果 → 存储分析 → 可选展示给用户
</flow>
<flow id="F3" name="数据导出流程">
  用户请求导出 → 验证身份 → 打包数据 → 加密传输 → 生成下载链接 → 用户下载 → 本地解密查看
</flow>
<flow id="F4" name="AI分身授权流程">
  用户查看AI分身功能 → 阅读授权说明 → 确认授权 → 系统收集训练数据 → 训练AI模型 → 激活AI分身功能
</flow>
</information-flow>

</information>

---

## 3. Context

<context>

### 3.1 Application Context

个人日记和习惯打卡应用，强调隐私保护和简约设计。支持文本记录、习惯追踪、日历视图，以及可选的AI辅助功能。

### 3.2 Technical Context

- 前端：Next.js 15 + TypeScript + Tailwind CSS
- 后端：Bun运行时，支持边缘计算
- 数据库：PostgreSQL，支持加密存储
- AI集成：Vercel AI SDK，支持多种模型
- 部署：支持Vercel等边缘平台
- 安全：端到端加密，本地优先架构

### 3.3 User Experience Context

- **情感目标**：提供安全、私密的心灵空间，让用户能够自由表达
- **使用场景**：日常记录、情绪宣泄、习惯养成、自我反思
- **交互风格**：极简设计，减少干扰，专注于内容本身
- **核心价值**："不要满不在乎地过日子，好像你可以活一千年似的"

</context>

---

## 4. Weight Matrix

<weights>
- 用户隐私保护：100%（最高优先级）
- 数据可导出性：90%（用户数据所有权）
- 使用便捷性：85%（降低记录门槛）
- AI功能准确性：70%（提供有价值的洞察）
- 系统性能：60%（响应速度要求）
</weights>

---

## 5. Verification Checklist

- [ ] 所有用户数据是否加密存储？
- [ ] 是否支持离线记录功能？
- [ ] 数据导出是否不依赖网络？
- [ ] AI分身功能是否需要明确授权？
- [ ] 用户是否可以随时撤销AI授权？
- [ ] 从打开应用到开始输入是否不超过3步？
- [ ] 是否所有实体都有唯一编码定义？
- [ ] 实体分类是否清晰明确？
- [ ] 信息流动是否考虑了隐私保护？
