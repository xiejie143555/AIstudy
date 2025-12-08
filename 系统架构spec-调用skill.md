# 日子系统架构规格书

## 1. 架构概览

**架构模式：** 分层架构 + 模块化设计 + 本地优先
**部署策略：** 边缘部署（Vercel/Cloudflare） + 本地PWA支持
**核心原则：** 隐私优先、离线可用、渐进增强

### 架构图

```
┌─────────────────────────────────────────────────────────────┐
│                    用户界面层 (UI Layer)                     │
│  ┌─────────────┬─────────────┬─────────────┬──────────────┐ │
│  │  记录组件   │  日历组件   │  习惯组件   │  设置组件    │ │
│  └──────┬──────┴──────┬──────┴──────┬──────┴──────┬───────┘ │
├────────┼─────────────┼─────────────┼─────────────┼─────────┤
│        │             │             │             │         │
│  ┌─────▼─────┐  ┌────▼─────┐  ┌────▼─────┐  ┌────▼─────┐   │
│  │  应用核心层 (Application Core)                          │ │
│  │  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐ │ │
│  │  │ 记录服务 │ │ 时间服务 │ │ 习惯服务 │ │ 导出服务 │ │ │
│  │  └────┬─────┘ └────┬─────┘ └────┬─────┘ └────┬─────┘ │ │
│  └──────┼────────────┼────────────┼────────────┼───────┘ │
│        │            │            │            │         │
├────────┼────────────┼────────────┼────────────┼─────────┤
│        │            │            │            │         │
│  ┌─────▼────────────▼────────────▼────────────▼───────┐   │
│  │              领域层 (Domain Layer)                 │   │
│  │  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌────────┐ │   │
│  │  │ 实体模型 │ │ 业务规则 │ │ 领域事件 │ │ 加密层 │ │   │
│  │  └──────────┘ └──────────┘ └──────────┘ └────────┘ │   │
│  └─────────────────────┬──────────────────────────────┘   │
├────────────────────────┼──────────────────────────────────┤
│                        │                                  │
│  ┌─────────────────────▼──────────────────────────────┐   │
│  │            基础设施层 (Infrastructure Layer)        │   │
│  │  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌────────┐ │   │
│  │  │本地存储  │ │远程同步  │ │AI服务    │ │认证服务│ │   │
│  │  │IndexedDB │ │Neon PG   │ │Vercel AI │ │Better │ │   │
│  │  └────┬─────┘ └────┬─────┘ └────┬─────┘ └────┬───┘ │   │
│  └───────┼────────────┼────────────┼────────────┼─────┘   │
│        ┌▼────────────▼────────────▼────────────▼┐        │
│        │         运行时层 (Runtime Layer)        │        │
│        │  ┌──────────┐ ┌──────────┐ ┌────────┐  │        │
│        │  │ Next.js  │ │   Bun    │ │  PWA   │  │        │
│        │  └──────────┘ └──────────┘ └────────┘  │        │
│        └──────────────────────────────────────────┘        │
└─────────────────────────────────────────────────────────────┘
```

## 2. 子系统设计

### 2.1 记录管理子系统

**职责：** 处理日记的创建、编辑、存储和检索

**组件：**
- **记录服务**：业务逻辑处理
- **编辑器组件**：富文本编辑界面
- **自动保存管理器**：30秒间隔自动保存
- **离线队列**：网络恢复后同步数据

**接口：**
- **输入：** 用户文本输入、日期选择、标签、心情等元数据
- **输出：** 加密的日记记录、保存状态、同步状态

**依赖：**
- **依赖于：** 加密子系统、本地存储子系统
- **被用于：** UI组件、导出子系统

### 2.2 时间导航子系统

**职责：** 提供基于时间的记录浏览和导航

**组件：**
- **日历服务**：日期计算和格式化
- **时间线组件**：可视化时间线展示
- **导航控制器**：日期切换逻辑
- **缓存管理器**：优化频繁访问的日期数据

**接口：**
- **输入：** 日期选择、视图模式（月/周）、导航操作
- **输出：** 日期范围内的记录列表、统计信息

### 2.3 习惯追踪子系统

**职责：** 管理用户习惯的创建、打卡和进度统计

**组件：**
- **习惯服务**：CRUD操作和逻辑处理
- **打卡组件**：一键打卡界面
- **统计引擎**：连续天数、完成率计算
- **提醒调度器**（可选）：打卡提醒

**接口：**
- **输入：** 习惯定义、打卡操作、时间范围
- **输出：** 打卡记录、统计数据、进度可视化

### 2.4 数据加密子系统

**职责：** 确保所有用户数据的端到端加密

**组件：**
- **加密服务**：AES-256-GCM加密实现
- **密钥管理器**：用户密钥生成和存储
- **安全随机数生成器**：初始化向量和盐值
- **合规验证器**：确保符合隐私约束

**接口：**
- **输入：** 明文数据、用户凭证
- **输出：** 密文数据、加密元数据

**关键实现：**
```typescript
// 加密服务示例
class EncryptionService {
  private readonly algorithm = 'aes-256-gcm';

  async encrypt(data: string, password: string): Promise<EncryptedData> {
    // 使用用户密码派生密钥
    const key = await this.deriveKey(password);
    // 生成随机IV
    const iv = crypto.getRandomValues(new Uint8Array(12));
    // 执行加密
    const encrypted = await crypto.subtle.encrypt(
      { name: this.algorithm, iv },
      key,
      new TextEncoder().encode(data)
    );
    return { encrypted, iv, salt };
  }
}
```

### 2.5 本地存储子系统

**职责：** 管理浏览器本地数据存储，支持离线功能

**组件：**
- **IndexedDB管理器**：结构化数据存储
- **存储适配器**：统一存储接口
- **同步协调器**：本地与云端数据同步
- **存储配额管理器**：监控和管理存储空间

**接口：**
- **输入：** 加密后的实体数据、同步指令
- **输出：** 本地数据查询结果、同步状态

### 2.6 AI集成子系统

**职责：** 提供可选的AI分析和洞察功能

**组件：**
- **AI服务封装**：Vercel AI SDK集成
- **授权管理器**：用户授权状态跟踪
- **分析调度器**：批量处理和分析任务
- **结果缓存**：避免重复分析相同内容

**接口：**
- **输入：** 用户授权、待分析内容、分析类型
- **输出：** 情感分析结果、主题提取、关键词等

**重要约束：**
- 必须获得用户明确授权（C3约束）
- 支持随时撤销授权
- 本地分析优先，减少数据传输

## 3. API设计

### 3.1 认证API

#### POST /api/auth/register
**描述：** 用户注册（可选，支持匿名使用）
**认证：** 无
**请求：**
```json
{
  "email": "user@example.com",
  "password": "secure-password",
  "metadata": {}
}
```
**响应：**
- 201: 注册成功，返回用户ID
- 400: 请求参数错误

#### POST /api/auth/login
**描述：** 用户登录
**认证：** 无
**请求：**
```json
{
  "email": "user@example.com",
  "password": "secure-password"
}
```
**响应：**
- 200: 登录成功，返回JWT令牌
- 401: 认证失败

### 3.2 记录管理API

#### GET /api/records
**描述：** 获取用户的日记记录列表
**认证：** 需要
**查询参数：**
- `startDate`: 开始日期（YYYY-MM-DD）
- `endDate`: 结束日期（YYYY-MM-DD）
- `tag`: 标签筛选
- `limit`: 返回数量限制

**响应：**
- 200: 记录列表
```json
{
  "records": [
    {
      "id": "record-id",
      "date": "2024-12-08",
      "title": "日记标题",
      "preview": "内容预览...",
      "tags": ["生活", "感悟"],
      "mood": "平静",
      "hasMore": true
    }
  ],
  "total": 42
}
```

#### POST /api/records
**描述：** 创建新记录
**认证：** 需要
**请求：**
```json
{
  "date": "2024-12-08",
  "title": "今天的思考",
  "content": "加密后的内容",
  "tags": ["生活"],
  "mood": "平静",
  "weather": "晴",
  "location": "北京"
}
```
**响应：**
- 201: 创建成功
- 400: 数据格式错误

#### PUT /api/records/:id
**描述：** 更新记录
**认证：** 需要（资源所有者）
**请求：** 部分更新字段
**响应：**
- 200: 更新成功
- 404: 记录不存在
- 403: 无权限

### 3.3 习惯管理API

#### GET /api/habits
**描述：** 获取用户的习惯列表
**认证：** 需要
**响应：**
```json
{
  "habits": [
    {
      "id": "habit-id",
      "name": "每日阅读",
      "frequency": "daily",
      "streak": 15,
      "totalCompleted": 45,
      "lastCheckin": "2024-12-07"
    }
  ]
}
```

#### POST /api/habits/:id/checkin
**描述：** 习惯打卡
**认证：** 需要
**请求：**
```json
{
  "date": "2024-12-08",
  "note": "读了《认知觉醒》第三章"
}
```
**响应：**
- 200: 打卡成功，返回新的连续天数

### 3.4 数据导出API

#### POST /api/export
**描述：** 导出用户数据
**认证：** 需要
**请求：**
```json
{
  "format": "markdown", // 或 "json"
  "include": ["records", "habits"],
  "dateRange": {
    "start": "2024-01-01",
    "end": "2024-12-31"
  }
}
```
**响应：**
- 200: 返回下载链接
```json
{
  "downloadUrl": "/api/export/download/:token",
  "expiresAt": "2024-12-08T12:00:00Z",
  "size": 1048576
}
```

### 3.5 AI分析API

#### POST /api/ai/analyze
**描述：** 请求AI分析记录内容
**认证：** 需要
**特殊要求：** 用户必须已授权AI功能
**请求：**
```json
{
  "recordIds": ["record-1", "record-2"],
  "analysisType": "emotion", // emotion, theme, keywords
  "options": {
    "detailed": true
  }
}
```
**响应：**
- 200: 分析结果
```json
{
  "analysis": {
    "type": "emotion",
    "result": {
      "overallTrend": "positive",
      "details": [
        {"date": "2024-12-07", "emotion": "happy", "score": 0.8}
      ]
    },
    "confidence": 0.85
  }
}
```
- 403: 用户未授权AI功能

## 4. 目录结构

```
src/
├── app/                              # Next.js App Router
│   ├── (auth)/                       # 认证相关页面
│   │   ├── login/page.tsx
│   │   └── register/page.tsx
│   ├── (main)/                       # 主应用页面
│   │   ├── page.tsx                  # 首页（今日记录）
│   │   ├── timeline/page.tsx         # 时间线视图
│   │   ├── habits/page.tsx           # 习惯管理
│   │   ├── settings/page.tsx         # 设置页面
│   │   └── export/page.tsx           # 数据导出
│   ├── api/                          # API路由
│   │   ├── auth/[...nextauth]/route.ts
│   │   ├── records/route.ts
│   │   ├── records/[id]/route.ts
│   │   ├── habits/route.ts
│   │   ├── habits/[id]/checkin/route.ts
│   │   ├── export/route.ts
│   │   └── ai/analyze/route.ts
│   ├── layout.tsx                    # 根布局
│   └── globals.css                   # 全局样式
├── components/                       # React组件
│   ├── ui/                          # 基础UI组件（shadcn/ui）
│   │   ├── button.tsx
│   │   ├── calendar.tsx
│   │   ├── dialog.tsx
│   │   └── textarea.tsx
│   ├── features/                    # 功能组件
│   │   ├── record/                  # 记录相关组件
│   │   │   ├── editor.tsx           # 富文本编辑器
│   │   │   ├── auto-save.tsx        # 自动保存指示器
│   │   │   └── mood-selector.tsx    # 心情选择器
│   │   ├── timeline/                # 时间线组件
│   │   │   ├── calendar-view.tsx    # 日历视图
│   │   │   ├── record-card.tsx      # 记录卡片
│   │   │   └── date-navigation.tsx  # 日期导航
│   │   ├── habits/                  # 习惯组件
│   │   │   ├── habit-list.tsx       # 习惯列表
│   │   │   ├── checkin-button.tsx   # 打卡按钮
│   │   │   └── streak-counter.tsx   # 连续天数计数器
│   │   └── export/                  # 导出组件
│   │       ├── export-dialog.tsx    # 导出对话框
│   │       └── format-selector.tsx  # 格式选择器
│   └── layout/                      # 布局组件
│       ├── header.tsx               # 页面头部
│       ├── navigation.tsx           # 导航菜单
│       └── footer.tsx               # 页面底部
├── lib/                             # 工具库
│   ├── db/                          # 数据库相关
│   │   ├── schema.ts                # Drizzle schema定义
│   │   ├── index.ts                 # 数据库连接
│   │   └── migrations/              # 迁移文件
│   ├── auth/                        # 认证相关
│   │   ├── auth-options.ts          # NextAuth配置
│   │   └── session.ts               # 会话管理
│   ├── encryption/                  # 加密相关
│   │   ├── crypto.ts                # 加密实现
│   │   ├── key-manager.ts           # 密钥管理
│   │   └── types.ts                 # 加密类型定义
│   ├── storage/                     # 存储相关
│   │   ├── local-storage.ts         # IndexedDB封装
│   │   ├── sync-manager.ts          # 同步管理
│   │   └── cache.ts                 # 缓存策略
│   ├── ai/                          # AI集成
│   │   ├── ai-client.ts             # Vercel AI SDK封装
│   │   ├── authorization.ts         # 授权管理
│   │   └── analyzers/               # 分析器实现
│   │       ├── emotion.ts           # 情感分析
│   │       ├── theme.ts             # 主题提取
│   │       └── keywords.ts          # 关键词提取
│   └── utils.ts                     # 通用工具函数
├── services/                        # 业务逻辑服务
│   ├── record.service.ts            # 记录管理服务
│   ├── habit.service.ts             # 习惯管理服务
│   ├── timeline.service.ts          # 时间线服务
│   ├── export.service.ts            # 导出服务
│   └── ai.service.ts                # AI分析服务
├── hooks/                           # React Hooks
│   ├── use-records.ts               # 记录数据管理
│   ├── use-habits.ts                # 习惯数据管理
│   ├── use-auto-save.ts             # 自动保存逻辑
│   ├── use-offline.ts               # 离线状态检测
│   └── use-encryption.ts            # 加密相关Hook
├── types/                           # TypeScript类型定义
│   ├── record.ts                    # 记录相关类型
│   ├── habit.ts                     # 习惯相关类型
│   ├── api.ts                       # API相关类型
│   └── encryption.ts                # 加密相关类型
├── constants/                       # 常量定义
│   ├── app.ts                       # 应用常量
│   ├── storage.ts                   # 存储相关常量
│   └── api.ts                       # API端点常量
└── middleware.ts                    # Next.js中间件
```

## 5. 安全架构

### 5.1 分层安全模型

```
┌─────────────────────────────────────────────────────────┐
│                  应用层安全                              │
│  ┌─────────────┬─────────────┬─────────────┬──────────┐ │
│  │  输入验证   │  XSS防护    │  CSRF防护   │ 权限控制 │ │
│  └──────┬──────┴──────┬──────┴──────┬──────┴──────┬───┘ │
├────────┼─────────────┼─────────────┼─────────────┼─────┤
│        │             │             │             │     │
│  ┌─────▼─────┐  ┌────▼─────┐  ┌────▼─────┐  ┌────▼───┐ │
│  │          数据层安全                                │ │
│  │  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌────────┐ │ │
│  │  │ 端到端加密│ │ 密钥管理  │ │ 数据脱敏  │ │ 审计日志│ │ │
│  │  └──────────┘ └──────────┘ └──────────┘ └────────┘ │ │
│  └─────────────────────┬──────────────────────────────┘ │
├────────────────────────┼──────────────────────────────────┤
│                        │                                  │
│  ┌─────────────────────▼──────────────────────────────┐   │
│  │              传输层安全                              │   │
│  │  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌────────┐ │   │
│  │  │HTTPS强制 │ │HSTS头部  │ │证书固定  │ │速率限制│ │   │
│  │  └──────────┘ └──────────┘ └──────────┘ └────────┘ │   │
│  └─────────────────────────────────────────────────────┘   │
└───────────────────────────────────────────────────────────┘
```

### 5.2 端到端加密实现

**加密流程：**
1. **密钥派生：** 使用用户密码 + PBKDF2派生加密密钥
2. **数据加密：** AES-256-GCM加密用户内容
3. **密钥存储：** 主密钥加密后存储，从不明文传输
4. **数据同步：** 仅同步密文，服务器无法解密

**代码示例：**
```typescript
// 加密服务
export class E2EEncryptionService {
  async encryptRecord(record: Record, password: string): Promise<EncryptedRecord> {
    // 1. 派生密钥
    const key = await this.deriveKey(password);

    // 2. 生成随机IV
    const iv = crypto.getRandomValues(new Uint8Array(12));

    // 3. 加密内容
    const encryptedContent = await crypto.subtle.encrypt(
      { name: 'AES-256-GCM', iv },
      key,
      new TextEncoder().encode(JSON.stringify(record))
    );

    // 4. 返回加密结果
    return {
      content: encryptedContent,
      iv,
      salt: this.getSalt(),
      timestamp: new Date()
    };
  }
}
```

### 5.3 认证与授权

**认证方案：** Better Auth集成
- 支持邮箱/密码认证
- 可选的匿名模式（本地生成身份）
- JWT令牌管理会话
- 刷新令牌机制

**授权策略：**
- **资源级授权：** 用户只能访问自己的数据
- **操作级授权：** 基于记录所有权的CRUD权限
- **AI功能授权：** 明确的用户同意机制

### 5.4 输入验证与防护

**验证策略：**
- **客户端验证：** 即时反馈，改善用户体验
- **服务端验证：** 最终安全保证
- **类型安全：** 使用Zod schema进行运行时验证

**防护措施：**
```typescript
// 输入验证示例
import { z } from 'zod';

const RecordSchema = z.object({
  title: z.string().max(100, '标题不能超过100字'),
  content: z.string().max(10000, '内容不能超过10000字'),
  date: z.string().regex(/^\d{4}-\d{2}-\d{2}$/),
  mood: z.enum(['开心', '平静', '悲伤', '愤怒', '焦虑']).optional(),
  tags: z.array(z.string().max(20)).max(10, '最多10个标签')
});
```

## 6. 技术决策记录

### ADR-001：本地优先架构选择

**状态：** 已接受

**背景：**
用户需要完全控制自己的数据，包括离线使用能力。传统的云优先架构无法满足隐私保护和离线需求。

**决策：**
采用本地优先架构，以IndexedDB为主要存储，PostgreSQL为可选的云端同步备份。

**后果：**
- ✅ 更好的离线支持
- ✅ 增强的隐私保护
- ✅ 更快的响应速度
- ❌ 增加了同步复杂性
- ❌ 需要处理数据冲突

### ADR-002：端到端加密实现

**状态：** 已接受

**背景：**
日记内容高度敏感，必须确保即使服务器被攻破，用户数据仍然安全。

**决策：**
在客户端使用Web Crypto API实现AES-256-GCM加密，服务器仅存储密文。

**后果：**
- ✅ 最高级别的数据保护
- ✅ 服务器无法读取用户内容
- ✅ 符合隐私法规要求
- ❌ 无法提供服务器端搜索
- ❌ 密码丢失无法恢复数据

### ADR-003：Next.js App Router选择

**状态：** 已接受

**背景：**
需要支持复杂的离线功能和优雅的服务器端渲染，同时保持良好的开发体验。

**决策：**
使用Next.js 15的App Router，配合React Server Components。

**后果：**
- ✅ 更好的性能优化
- ✅ 内置的PWA支持
- ✅ 服务器组件减少JS包大小
- ❌ 学习曲线较陡峭
- ❌ 某些库兼容性待验证

### ADR-004：数据库双重存储策略

**状态：** 已接受

**背景：**
需要同时支持离线存储和云端备份，保证数据的可用性和持久性。

**决策：**
本地使用IndexedDB，云端使用PostgreSQL（Neon），通过同步服务协调。

**后果：**
- ✅ 离线优先体验
- ✅ 数据多重备份
- ✅ 灵活的部署选项
- ❌ 同步逻辑复杂
- ❌ 需要处理冲突解决

### ADR-005：AI功能可选设计

**状态：** 已接受

**背景：**
AI分析功能涉及用户隐私，必须确保用户完全控制。

**决策：**
AI功能默认关闭，需要用户明确授权才能使用，且可随时撤销。

**后果：**
- ✅ 尊重用户隐私选择
- ✅ 符合法规要求
- ✅ 建立用户信任
- ❌ 功能使用率可能较低
- ❌ 实现复杂度增加

## 7. 性能优化策略

### 7.1 加载性能

**代码分割：**
- 按路由自动分割
- 动态导入大型组件
- 第三方库按需加载

**资源优化：**
```typescript
// 动态导入示例
const RichEditor = dynamic(() => import('@/components/rich-editor'), {
  loading: () => <Spinner />,
  ssr: false // 编辑器只在客户端渲染
});
```

### 7.2 运行时性能

**渲染优化：**
- React.memo防止不必要的重渲染
- useMemo缓存计算结果
- useCallback缓存函数引用

**数据获取优化：**
- SWR进行智能缓存和重新验证
- 分页加载大量数据
- 乐观更新提升感知性能

### 7.3 存储性能

**IndexedDB优化：**
- 使用复合索引加速查询
- 批量操作减少事务开销
- 合理设置数据过期策略

```typescript
// 复合索引示例
export const records = sqliteTable('records', {
  id: text('id').primaryKey(),
  userId: text('user_id').notNull(),
  date: text('date').notNull(),
  createdAt: integer('created_at', { mode: 'timestamp' }).notNull(),
}, (table) => ({
  userDateIdx: index('user_date_idx').on(table.userId, table.date),
}));
```

## 8. 监控与健康检查

### 8.1 应用性能监控

**核心指标：**
- 首次内容绘制（FCP）
- 最大内容绘制（LCP）
- 首次输入延迟（FID）
- 累积布局偏移（CLS）

**实现方案：**
```typescript
// Web Vitals监控
import { onCLS, onFID, onLCP } from 'web-vitals';

function reportWebVitals(metric: any) {
  // 发送到分析服务
  analytics.track('web_vital', metric);
}

onCLS(reportWebVitals);
onFID(reportWebVitals);
onLCP(reportWebVitals);
```

### 8.2 错误监控

**错误捕获：**
- 全局错误处理：window.onerror
- Promise拒绝：unhandledrejection
- React错误边界：ErrorBoundary组件

**错误报告：**
```typescript
// 错误边界示例
export default function ErrorBoundary({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <Sentry.ErrorBoundary
      fallback={({ error, resetError }) => (
        <div>
          <h2>出错了</h2>
          <button onClick={resetError}>重试</button>
        </div>
      )}
    >
      {children}
    </Sentry.ErrorBoundary>
  );
}
```

### 8.3 业务指标监控

**用户行为追踪：**
- 记录创建频率
- 功能使用统计
- 用户留存率
- 导出功能使用率

**隐私保护：**
- 匿名化用户ID
- 本地聚合统计
- 不追踪具体内容

## 9. 部署架构

### 9.1 边缘部署

**部署平台：** Vercel Edge Functions
**优势：**
- 全球CDN分发
- 边缘计算能力
- 自动扩展
- 与Next.js深度集成

**配置示例：**
```json
// vercel.json
{
  "functions": {
    "app/api/ai/analyze/route.ts": {
      "maxDuration": 30
    }
  },
  "regions": ["hkg1", "sin1", "nrt1"]
}
```

### 9.2 数据库部署

**本地开发：** PostgreSQL Docker容器
**生产环境：** Neon Serverless PostgreSQL
**优势：**
- 自动备份
- 分支支持
- 按使用计费
- 与Prisma/Drizzle兼容

### 9.3 环境配置

**环境变量：**
```bash
# 数据库
DATABASE_URL="postgresql://..."

# 认证
NEXTAUTH_URL="https://your-app.com"
NEXTAUTH_SECRET="..."
BETTER_AUTH_SECRET="..."

# AI服务
OPENAI_API_KEY="..."
ANTHROPIC_API_KEY="..."

# 加密
MASTER_KEY_SALT="..."
```

## 10. 合规与约束实现

### 10.1 隐私约束实现

**C1 - 用户数据隐私保护：**
- ✅ 端到端加密实现
- ✅ 本地数据处理优先
- ✅ 最小权限访问控制
- ✅ 数据匿名化处理

**C2 - 数据可导出性：**
- ✅ Markdown/JSON格式支持
- ✅ 完全离线导出功能
- ✅ 批量导出能力
- ✅ 数据完整性保证

**C3 - AI授权机制：**
- ✅ 明确的授权界面
- ✅ 详细的授权说明
- ✅ 一键撤销功能
- ✅ 授权状态管理

**C4 - 离线记录能力：**
- ✅ IndexedDB本地存储
- ✅ Service Worker缓存
- ✅ 离线检测处理
- ✅ 同步冲突解决

### 10.2 性能指标保证

**响应时间：**
- ✅ 3秒内启动完成
- ✅ 30秒自动保存
- ✅ 离线优先架构
- ✅ 增量同步策略

**可靠性：**
- ✅ 异常恢复机制
- ✅ 数据多重备份
- ✅ 错误边界处理
- ✅ 优雅降级支持

---

**文档版本：** 1.0.0
**创建日期：** 2024-12-08
**维护者：** 技术团队
**审查状态：** 待审查
**架构师：** Claude Code