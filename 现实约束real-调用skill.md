---
name: project-real
description: Real-world constraints for the project
---

# 日子 - 现实约束 (Real)

<meta>
  <document-id>day-real</document-id>
  <version>1.0.0</version>
  <project>日子</project>
  <type>Reality Constraints</type>
  <created>2024-12-08</created>
</meta>

## Document Purpose

定义"日子"个人日记应用必须遵守的现实世界约束，确保用户隐私保护和数据安全。

<constraints>

## Required Constraints (Maximum 4)

<constraint required="true" id="C1">
<title>用户数据隐私保护</title>
<description>用户的所有日记、打卡记录等个人数据必须加密存储，仅用户本人可访问</description>
<rationale>日记内容属于高度敏感的个人隐私信息，一旦泄露将造成严重的精神伤害</rationale>
<violation-consequence>用户隐私泄露可能导致法律诉讼和平台信任崩塌</violation-consequence>
</constraint>

<constraint required="true" id="C2">
<title>数据可导出性</title>
<description>用户必须能够随时将个人数据导出为标准格式（Markdown或JSON），且导出过程不得依赖网络连接</description>
<rationale>用户对自己的数据拥有完全所有权，应确保数据可移植性和平台无关性</rationale>
<violation-consequence>用户数据被锁定在平台内，无法迁移或备份</violation-consequence>
</constraint>

<constraint required="true" id="C3">
<title>AI数字分身授权机制</title>
<description>基于用户日记内容生成AI数字分身前，必须获得用户的明确授权，且用户可随时撤销授权</description>
<rationale>AI分身涉及用户人格特征的数字化再现，必须尊重用户对其数字人格的完全控制权</rationale>
<violation-consequence>未经授权创建AI分身将严重侵犯用户人格权，可能面临法律风险</violation-consequence>
</constraint>

<constraint required="true" id="C4">
<title>离线记录能力</title>
<description>从打开应用到开始输入日记的路径必须最短，且必须支持离线环境下的记录功能</description>
<rationale>日记记录往往具有即时性需求，复杂的操作流程会阻碍用户记录灵感</rationale>
<violation-consequence>用户体验下降，可能导致用户放弃使用</violation-consequence>
</constraint>

## Optional Constraints (Maximum 3)

<constraint required="false" id="C5">
<title>数据最小化原则</title>
<description>仅收集应用功能必需的用户数据，避免过度收集个人信息</description>
<rationale>减少数据收集可以降低隐私泄露风险和合规成本</rationale>
</constraint>

<constraint required="false" id="C6">
<title>本地优先架构</title>
<description>优先在本地处理数据，网络同步作为可选功能</description>
<rationale>本地处理可以最大化保护用户隐私，减少对网络的依赖</rationale>
</constraint>

</constraints>

## Technical Environment

<environment>
<stack>
- Frontend: Next.js 15, TypeScript, Tailwind CSS, shadcn/ui
- Runtime: Bun
- Database: PostgreSQL/Neon/Supabase
- ORM: Drizzle ORM
- Auth: Better Auth
- AI SDK: Vercel AI SDK
- Python: uv environment manager
</stack>
</environment>

## Constraint Checklist

- [x] C1: 用户数据隐私保护 - 数据加密存储，仅用户本人访问
- [x] C2: 数据可导出性 - 支持Markdown/JSON格式导出
- [x] C3: AI数字分身授权机制 - 需明确授权，可撤销
- [x] C4: 离线记录能力 - 最短路径，支持离线
- [ ] C5: 数据最小化原则 - 仅收集必需数据
- [ ] C6: 本地优先架构 - 本地处理优先