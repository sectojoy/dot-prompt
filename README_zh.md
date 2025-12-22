# ZelonAI Prompt 模板规范 (`.prompt`)

> **版本**: 1.0.0 | **作者**: SectoJoy | **许可证**: MIT

一种基于 **Front-matter + Markdown** 的 AI 提示词模板规范，专为 Prompt 类应用设计。

## 文件格式

```
---
[YAML Front Matter]
---

[Markdown 模板内容，支持 {{变量}} 和条件渲染]
```

文件扩展名：`.prompt`

---

## 一、Front Matter 结构

### 1.1 核心元数据

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `title` | string | ✅ | 模板标题 |
| `description` | string | - | 模板描述 |
| `version` | string | - | 语义化版本号 (如 `1.0.0`) |

### 1.2 最小示例

```yaml
---
title: "周报生成器"
---

请帮我写一份周报，内容如下：
{{ content }}
```

### 1.3 完整示例

```yaml
---
title: "小红书爆款生成器"
description: "生成 emoji 风格的种草文案"
version: "1.2.0"

inputs:
  - key: "product_name"
    type: "text"
    label: "产品名称"
    placeholder: "例如：iPhone 15 Pro"
    required: true

  - key: "features"
    type: "longText"
    label: "产品卖点"
    placeholder: "粘贴产品参数或卖点..."
    default: "性价比高，颜值高"

  - key: "style"
    type: "select"
    label: "文案风格"
    options:
      - "😍 疯狂种草"
      - "🤔 理性测评"
      - "✨ 文艺清新"
    default: "😍 疯狂种草"

  - key: "include_tags"
    type: "toggle"
    label: "添加标签"
    default: true
    trueLabel: "添加话题标签"
    falseLabel: "不加标签"
---

你是一个小红书博主。请推荐 {{ product_name }}。

卖点如下：
{{ features }}

请使用【{{ style }}】的语气风格。

{{#if include_tags}}
请在文末附带 5 个相关的热门 hashtag。
{{/if}}
```

---

## 二、变量输入定义 (`inputs`)

`inputs` 是一个数组，每个元素定义一个用户输入变量。

### 2.1 通用属性

所有变量类型都支持以下属性：

| 属性 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `key` | string | ✅ | 变量标识符，对应模板中的 `{{ key }}` |
| `type` | string | ✅ | 变量类型 |
| `label` | string | - | 显示标签（默认使用 key） |
| `default` | any | - | 默认值 |
| `placeholder` | string | - | 输入提示文本 |
| `required` | boolean | - | 是否必填（默认 `false`） |
| `help` | string | - | 帮助说明文本 |

### 2.2 变量类型详解

#### `text` - 单行文本

适用于短文本输入，如名称、标题等。

```yaml
- key: "name"
  type: "text"
  label: "姓名"
  placeholder: "请输入姓名"
  required: true
  pattern: "^[\\u4e00-\\u9fa5a-zA-Z]{2,20}$"
  patternError: "请输入 2-20 个字符的中英文姓名"
  minLength: 2
  maxLength: 20
```

**专属属性**：

| 属性 | 类型 | 说明 |
|------|------|------|
| `pattern` | string | 正则表达式验证 |
| `patternError` | string | 验证失败提示信息 |
| `minLength` | integer | 最小长度 |
| `maxLength` | integer | 最大长度 |

---

#### `longText` - 多行文本

适用于长文本输入，如描述、内容等。

```yaml
- key: "content"
  type: "longText"
  label: "文章内容"
  placeholder: "请输入或粘贴内容..."
  required: true
  minLength: 50
  maxLength: 5000
  rows: 6
```

**专属属性**：

| 属性 | 类型 | 说明 |
|------|------|------|
| `minLength` | integer | 最小长度 |
| `maxLength` | integer | 最大长度 |
| `rows` | integer | 显示行数（UI 提示） |

---

#### `select` - 选择器

适用于从预定义选项中选择。

```yaml
# 简单数组格式（value = label）
- key: "mood"
  type: "select"
  label: "心情"
  options:
    - "开心"
    - "平静"
    - "焦虑"
  default: "开心"

# 对象数组格式（支持 value/label 分离）
- key: "priority"
  type: "select"
  label: "优先级"
  options:
    - value: "high"
      label: "🔴 高优先级"
      description: "需要立即处理"
    - value: "medium"
      label: "🟡 中优先级"
      description: "本周内处理"
    - value: "low"
      label: "🟢 低优先级"
      description: "有空再处理"
  default: "medium"

# 多选模式
- key: "tags"
  type: "select"
  label: "标签"
  multiple: true
  options:
    - "技术"
    - "生活"
    - "旅行"
```

**专属属性**：

| 属性 | 类型 | 说明 |
|------|------|------|
| `options` | array | **必填**，选项列表 |
| `multiple` | boolean | 是否允许多选（默认 `false`） |

**选项格式**：

```yaml
# 格式一：字符串数组
options: ["选项A", "选项B", "选项C"]

# 格式二：对象数组
options:
  - value: "a"      # 实际值
    label: "选项 A"  # 显示文本
    description: "说明文字"  # 可选描述
```

---

#### `toggle` - 开关

适用于布尔值选择。

```yaml
- key: "is_public"
  type: "toggle"
  label: "公开发布"
  default: false
  trueLabel: "公开可见"
  falseLabel: "仅自己可见"
```

**专属属性**：

| 属性 | 类型 | 说明 |
|------|------|------|
| `trueLabel` | string | 开启状态显示文本 |
| `falseLabel` | string | 关闭状态显示文本 |

**模板中的值**：`true` / `false` (布尔类型)

---

#### `number` - 数字

适用于数值输入。

```yaml
- key: "count"
  type: "number"
  label: "数量"
  default: 5
  min: 1
  max: 100
  step: 1
```

**专属属性**：

| 属性 | 类型 | 说明 |
|------|------|------|
| `min` | number | 最小值 |
| `max` | number | 最大值 |
| `step` | number | 步进值 |

---

#### `date` - 日期

适用于日期选择。

```yaml
- key: "deadline"
  type: "date"
  label: "截止日期"
  default: "today"  # 特殊值：today, tomorrow
  format: "YYYY-MM-DD"
  minDate: "today"
  maxDate: "+30d"   # 相对日期：+30d 表示30天后
```

**专属属性**：

| 属性 | 类型 | 说明 |
|------|------|------|
| `format` | string | 日期格式（默认 `YYYY-MM-DD`） |
| `minDate` | string | 最早可选日期 |
| `maxDate` | string | 最晚可选日期 |

---

#### `email` - 邮箱

内置邮箱格式验证。

```yaml
- key: "email"
  type: "email"
  label: "邮箱地址"
  placeholder: "example@domain.com"
  required: true
```

---

#### `url` - 链接

内置 URL 格式验证。

```yaml
- key: "website"
  type: "url"
  label: "网站地址"
  placeholder: "https://example.com"
```

---

### 2.3 变量类型速查表

| 类型 | 描述 | UI 组件 | 输出值类型 |
|------|------|---------|-----------|
| `text` | 单行文本 | TextField | string |
| `longText` | 多行文本 | TextEditor | string |
| `select` | 单选/多选 | Picker/Menu | string / string[] |
| `toggle` | 布尔开关 | Toggle | boolean |
| `number` | 数字输入 | NumberField | number |
| `date` | 日期选择 | DatePicker | string (ISO) |
| `email` | 邮箱输入 | TextField | string |
| `url` | 链接输入 | TextField | string |

---

## 三、模板语法

### 3.1 变量插值

使用双花括号 `{{ }}` 插入变量值：

```markdown
你好，{{ name }}！

今天的任务是：{{ task }}
```

**变量命名规则**：
- 支持字母、数字、下划线：`[a-zA-Z_][a-zA-Z0-9_]*`
- 支持中文等 Unicode 字符：`{{ 项目名称 }}`

### 3.2 条件渲染

根据变量值决定是否显示某段内容：

```markdown
{{#if include_summary}}
## 摘要
这是摘要部分，仅在勾选时显示。
{{/if}}
```

**支持的条件语法**：

```markdown
{{#if variable}}
  当 variable 为 truthy 时显示
{{/if}}

{{#if variable}}
  条件为真
{{else}}
  条件为假
{{/if}}

{{#unless variable}}
  当 variable 为 falsy 时显示
{{/unless}}
```

**Truthy/Falsy 判定规则**：
- **Falsy**: `false`, `""` (空字符串), `0`, `null`, `undefined`
- **Truthy**: 其他所有值

### 3.3 循环渲染

遍历数组类型的变量（如多选 select）：

```markdown
{{#each selected_tags}}
- {{ this }}
{{/each}}

{{#each items}}
- {{ this.name }}: {{ this.value }}
{{/each}}
```

### 3.4 默认值

当变量未提供时使用默认值：

```markdown
{{ name | default: "匿名用户" }}
{{ count | default: 10 }}
```

### 3.5 转义

如需显示字面量 `{{`，使用反斜杠转义：

```markdown
使用 \{{ variable \}} 语法插入变量。
```

---

## 四、完整示例

### 4.1 周报生成器

```yaml
---
title: "周报生成器"
description: "根据本周工作内容生成专业周报"
version: "1.0.0"

inputs:
  - key: "week_number"
    type: "text"
    label: "周次"
    placeholder: "例如：第 50 周"
    required: true

  - key: "accomplishments"
    type: "longText"
    label: "本周完成"
    placeholder: "列出本周完成的主要工作..."
    required: true

  - key: "in_progress"
    type: "longText"
    label: "进行中"
    placeholder: "列出正在进行的工作..."

  - key: "blockers"
    type: "longText"
    label: "遇到的问题"
    placeholder: "如无可留空"

  - key: "next_week"
    type: "longText"
    label: "下周计划"
    placeholder: "列出下周的工作计划..."

  - key: "mood"
    type: "select"
    label: "整体状态"
    options:
      - value: "good"
        label: "🟢 进展顺利"
      - value: "normal"
        label: "🟡 正常推进"
      - value: "blocked"
        label: "🔴 遇到阻塞"
    default: "normal"

  - key: "include_metrics"
    type: "toggle"
    label: "包含数据指标"
    default: false
    trueLabel: "添加量化数据"
    falseLabel: "纯文字描述"
---

# {{ week_number }} 工作周报

**整体状态**: {{ mood }}

## ✅ 本周完成

{{ accomplishments }}

{{#if in_progress}}
## 🔄 进行中

{{ in_progress }}
{{/if}}

{{#if blockers}}
## ⚠️ 遇到的问题

{{ blockers }}
{{/if}}

{{#if next_week}}
## 📋 下周计划

{{ next_week }}
{{/if}}

{{#if include_metrics}}
## 📊 数据指标

请补充相关的量化数据指标。
{{/if}}
```

### 4.2 代码审查助手

```yaml
---
title: "代码审查助手"
description: "AI 辅助代码审查，发现潜在问题"
version: "1.0.0"

inputs:
  - key: "code"
    type: "longText"
    label: "待审查代码"
    placeholder: "粘贴需要审查的代码..."
    required: true
    rows: 15

  - key: "language"
    type: "select"
    label: "编程语言"
    options:
      - "Swift"
      - "Python"
      - "JavaScript"
      - "TypeScript"
      - "Go"
      - "Rust"
      - "Java"
      - "其他"
    default: "Swift"

  - key: "focus_areas"
    type: "select"
    label: "关注重点"
    multiple: true
    options:
      - value: "security"
        label: "🔒 安全性"
      - value: "performance"
        label: "⚡ 性能"
      - value: "readability"
        label: "📖 可读性"
      - value: "best_practices"
        label: "✨ 最佳实践"

  - key: "strict_mode"
    type: "toggle"
    label: "严格模式"
    default: false
    trueLabel: "严格审查（包含建议优化）"
    falseLabel: "常规审查（仅关键问题）"
---

请审查以下 {{ language }} 代码：

```{{ language | lowercase }}
{{ code }}
```

{{#if focus_areas}}
**审查重点**：
{{#each focus_areas}}
- {{ this }}
{{/each}}
{{/if}}

{{#if strict_mode}}
请进行严格审查，包括：
1. 关键问题（必须修复）
2. 建议优化（推荐改进）
3. 代码风格（格式规范）
{{else}}
请关注以下方面：
1. 潜在的 Bug 或错误
2. 安全漏洞
3. 明显的性能问题
{{/if}}

请以 Markdown 格式输出审查报告。
```

---

## 五、与现有规范的兼容性

### 5.1 对比表

| 特性 | 本规范 | Dotprompt | LangChain | Fabric |
|------|:------:|:---------:|:---------:|:------:|
| Front-matter | ✅ | ✅ | ❌ | ❌ |
| UI 控件定义 | ✅ | ❌ | ❌ | ❌ |
| 条件渲染 | ✅ | ✅ | ❌ | ❌ |
| 循环渲染 | ✅ | ✅ | ❌ | ❌ |
| 变量验证 | ✅ | ✅ | ❌ | ❌ |
| Markdown 内容 | ✅ | ✅ | ❌ | ✅ |

### 5.2 从其他格式导入

**从 LangChain YAML**：

```yaml
# LangChain
_type: prompt
input_variables: ["topic"]
template: "Tell me about {topic}"

# 转换为本规范
---
title: "Topic Generator"
inputs:
  - key: "topic"
    type: "text"
---
Tell me about {{ topic }}
```

**从 Fabric Pattern**：

```markdown
# Fabric (system.md)
# IDENTITY
You are an expert...

# STEPS
1. Analyze input
2. Generate output

# 转换为本规范
---
title: "Expert Assistant"
inputs:
  - key: "input"
    type: "longText"
---
# IDENTITY
You are an expert...

# STEPS
1. Analyze: {{ input }}
2. Generate output
```

---

## 六、JSON Schema

完整的 JSON Schema 定义：

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "title": "Prompt Template",
  "type": "object",
  "required": ["title"],
  "properties": {
    "title": { "type": "string", "minLength": 1 },
    "description": { "type": "string" },
    "version": { "type": "string", "pattern": "^\\d+\\.\\d+\\.\\d+$" },
    "inputs": {
      "type": "array",
      "items": {
        "type": "object",
        "required": ["key", "type"],
        "properties": {
          "key": { "type": "string", "pattern": "^[a-zA-Z_][a-zA-Z0-9_]*$" },
          "type": { "enum": ["text", "longText", "select", "toggle", "number", "date", "email", "url"] },
          "label": { "type": "string" },
          "default": {},
          "placeholder": { "type": "string" },
          "required": { "type": "boolean" },
          "help": { "type": "string" },
          "pattern": { "type": "string" },
          "patternError": { "type": "string" },
          "minLength": { "type": "integer" },
          "maxLength": { "type": "integer" },
          "options": { "type": "array" },
          "multiple": { "type": "boolean" },
          "trueLabel": { "type": "string" },
          "falseLabel": { "type": "string" },
          "min": { "type": "number" },
          "max": { "type": "number" },
          "step": { "type": "number" },
          "format": { "type": "string" },
          "minDate": { "type": "string" },
          "maxDate": { "type": "string" },
          "rows": { "type": "integer" }
        }
      }
    }
  }
}
```

---

## 七、参考资料

- [Google Dotprompt](https://genkit.dev/docs/dotprompt/)
- [Microsoft Semantic Kernel](https://learn.microsoft.com/en-us/semantic-kernel/concepts/prompts/)
- [LangChain Prompts](https://python.langchain.com/docs/concepts/prompt_templates/)
- [Anthropic MCP](https://modelcontextprotocol.io/docs/concepts/prompts)
- [Fabric Patterns](https://github.com/danielmiessler/fabric)
