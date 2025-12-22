# ZelonAI Prompt Template Specification (`.prompt`)

> **Version**: 1.0.0 | **Author**: SectoJoy | **License**: MIT

A **Front-matter + Markdown** based AI prompt template specification, designed for prompt-based applications.

## File Format

```
---
[YAML Front Matter]
---

[Markdown template content with {{variables}} and conditional rendering]
```

File extension: `.prompt`

---

## 1. Front Matter Structure

### 1.1 Core Metadata

| Field | Type | Required | Description |
|-------|------|:--------:|-------------|
| `title` | string | ✅ | Template title |
| `description` | string | - | Template description |
| `version` | string | - | Semantic version (e.g., `1.0.0`) |

### 1.2 Minimal Example

```yaml
---
title: "Weekly Report Generator"
---

Please write a weekly report with the following content:
{{ content }}
```

### 1.3 Complete Example

```yaml
---
title: "Social Media Post Generator"
description: "Generate engaging social media posts with emojis"
version: "1.2.0"

inputs:
  - key: "product_name"
    type: "text"
    label: "Product Name"
    placeholder: "e.g., iPhone 15 Pro"
    required: true

  - key: "features"
    type: "longText"
    label: "Product Features"
    placeholder: "Paste product specs or highlights..."
    default: "Great value, beautiful design"

  - key: "style"
    type: "select"
    label: "Writing Style"
    options:
      - "😍 Enthusiastic"
      - "🤔 Analytical"
      - "✨ Elegant"
    default: "😍 Enthusiastic"

  - key: "include_tags"
    type: "toggle"
    label: "Add Hashtags"
    default: true
    trueLabel: "Include hashtags"
    falseLabel: "No hashtags"
---

You are a social media influencer. Please recommend {{ product_name }}.

Key features:
{{ features }}

Please use a【{{ style }}】tone.

{{#if include_tags}}
Please add 5 relevant trending hashtags at the end.
{{/if}}
```

---

## 2. Variable Input Definition (`inputs`)

`inputs` is an array where each element defines a user input variable.

### 2.1 Common Properties

All variable types support the following properties:

| Property | Type | Required | Description |
|----------|------|:--------:|-------------|
| `key` | string | ✅ | Variable identifier, corresponds to `{{ key }}` in template |
| `type` | string | ✅ | Variable type |
| `label` | string | - | Display label (defaults to key) |
| `default` | any | - | Default value |
| `placeholder` | string | - | Input placeholder text |
| `required` | boolean | - | Whether input is required (default `false`) |
| `help` | string | - | Help text description |

### 2.2 Variable Types

#### `text` - Single-line Text

For short text input like names, titles, etc.

```yaml
- key: "name"
  type: "text"
  label: "Name"
  placeholder: "Enter your name"
  required: true
  pattern: "^[a-zA-Z ]{2,50}$"
  patternError: "Please enter 2-50 alphabetic characters"
  minLength: 2
  maxLength: 50
```

**Type-specific Properties**:

| Property | Type | Description |
|----------|------|-------------|
| `pattern` | string | Regex validation pattern |
| `patternError` | string | Validation error message |
| `minLength` | integer | Minimum length |
| `maxLength` | integer | Maximum length |

---

#### `longText` - Multi-line Text

For long text input like descriptions, content, etc.

```yaml
- key: "content"
  type: "longText"
  label: "Article Content"
  placeholder: "Enter or paste content..."
  required: true
  minLength: 50
  maxLength: 5000
  rows: 6
```

**Type-specific Properties**:

| Property | Type | Description |
|----------|------|-------------|
| `minLength` | integer | Minimum length |
| `maxLength` | integer | Maximum length |
| `rows` | integer | Display row count (UI hint) |

---

#### `select` - Selection

For selecting from predefined options.

```yaml
# Simple array format (value = label)
- key: "mood"
  type: "select"
  label: "Mood"
  options:
    - "Happy"
    - "Calm"
    - "Anxious"
  default: "Happy"

# Object array format (supports value/label separation)
- key: "priority"
  type: "select"
  label: "Priority"
  options:
    - value: "high"
      label: "🔴 High Priority"
      description: "Needs immediate attention"
    - value: "medium"
      label: "🟡 Medium Priority"
      description: "Handle this week"
    - value: "low"
      label: "🟢 Low Priority"
      description: "When time permits"
  default: "medium"

# Multiple selection mode
- key: "tags"
  type: "select"
  label: "Tags"
  multiple: true
  options:
    - "Technology"
    - "Lifestyle"
    - "Travel"
```

**Type-specific Properties**:

| Property | Type | Description |
|----------|------|-------------|
| `options` | array | **Required**, list of options |
| `multiple` | boolean | Allow multiple selection (default `false`) |

**Options Format**:

```yaml
# Format 1: String array
options: ["Option A", "Option B", "Option C"]

# Format 2: Object array
options:
  - value: "a"           # Actual value
    label: "Option A"    # Display text
    description: "Description"  # Optional description
```

---

#### `toggle` - Boolean Switch

For boolean value selection.

```yaml
- key: "is_public"
  type: "toggle"
  label: "Public Visibility"
  default: false
  trueLabel: "Publicly visible"
  falseLabel: "Private only"
```

**Type-specific Properties**:

| Property | Type | Description |
|----------|------|-------------|
| `trueLabel` | string | Label when enabled |
| `falseLabel` | string | Label when disabled |

**Template Value**: `true` / `false` (boolean type)

---

#### `number` - Numeric Input

For numeric value input.

```yaml
- key: "count"
  type: "number"
  label: "Quantity"
  default: 5
  min: 1
  max: 100
  step: 1
```

**Type-specific Properties**:

| Property | Type | Description |
|----------|------|-------------|
| `min` | number | Minimum value |
| `max` | number | Maximum value |
| `step` | number | Step increment |

---

#### `date` - Date Picker

For date selection.

```yaml
- key: "deadline"
  type: "date"
  label: "Deadline"
  default: "today"  # Special values: today, tomorrow
  format: "YYYY-MM-DD"
  minDate: "today"
  maxDate: "+30d"   # Relative date: +30d means 30 days later
```

**Type-specific Properties**:

| Property | Type | Description |
|----------|------|-------------|
| `format` | string | Date format (default `YYYY-MM-DD`) |
| `minDate` | string | Earliest selectable date |
| `maxDate` | string | Latest selectable date |

---

#### `email` - Email Input

Built-in email format validation.

```yaml
- key: "email"
  type: "email"
  label: "Email Address"
  placeholder: "example@domain.com"
  required: true
```

---

#### `url` - URL Input

Built-in URL format validation.

```yaml
- key: "website"
  type: "url"
  label: "Website"
  placeholder: "https://example.com"
```

---

### 2.3 Variable Types Quick Reference

| Type | Description | UI Component | Output Type |
|------|-------------|--------------|-------------|
| `text` | Single-line text | TextField | string |
| `longText` | Multi-line text | TextEditor | string |
| `select` | Single/Multi select | Picker/Menu | string / string[] |
| `toggle` | Boolean switch | Toggle | boolean |
| `number` | Numeric input | NumberField | number |
| `date` | Date picker | DatePicker | string (ISO) |
| `email` | Email input | TextField | string |
| `url` | URL input | TextField | string |

---

## 3. Template Syntax

### 3.1 Variable Interpolation

Use double curly braces `{{ }}` to insert variable values:

```markdown
Hello, {{ name }}!

Today's task is: {{ task }}
```

**Variable Naming Rules**:
- Supports letters, numbers, underscores: `[a-zA-Z_][a-zA-Z0-9_]*`
- Supports Unicode characters (Chinese, etc.): `{{ project_name }}`

### 3.2 Conditional Rendering

Conditionally display content based on variable values:

```markdown
{{#if include_summary}}
## Summary
This section only appears when checked.
{{/if}}
```

**Supported Conditional Syntax**:

```markdown
{{#if variable}}
  Displayed when variable is truthy
{{/if}}

{{#if variable}}
  Condition is true
{{else}}
  Condition is false
{{/if}}

{{#unless variable}}
  Displayed when variable is falsy
{{/unless}}
```

**Truthy/Falsy Rules**:
- **Falsy**: `false`, `""` (empty string), `0`, `null`, `undefined`
- **Truthy**: All other values

### 3.3 Loop Rendering

Iterate over array-type variables (e.g., multi-select):

```markdown
{{#each selected_tags}}
- {{ this }}
{{/each}}

{{#each items}}
- {{ this.name }}: {{ this.value }}
{{/each}}
```

### 3.4 Default Values

Use default value when variable is not provided:

```markdown
{{ name | default: "Anonymous" }}
{{ count | default: 10 }}
```

### 3.5 Escaping

To display literal `{{`, use backslash escape:

```markdown
Use \{{ variable \}} syntax to insert variables.
```

---

## 4. Complete Examples

### 4.1 Weekly Report Generator

```yaml
---
title: "Weekly Report Generator"
description: "Generate professional weekly reports from work content"
version: "1.0.0"

inputs:
  - key: "week_number"
    type: "text"
    label: "Week Number"
    placeholder: "e.g., Week 50"
    required: true

  - key: "accomplishments"
    type: "longText"
    label: "Accomplishments"
    placeholder: "List main work completed this week..."
    required: true

  - key: "in_progress"
    type: "longText"
    label: "In Progress"
    placeholder: "List work currently in progress..."

  - key: "blockers"
    type: "longText"
    label: "Blockers"
    placeholder: "Leave empty if none"

  - key: "next_week"
    type: "longText"
    label: "Next Week Plan"
    placeholder: "List next week's work plan..."

  - key: "status"
    type: "select"
    label: "Overall Status"
    options:
      - value: "good"
        label: "🟢 On Track"
      - value: "normal"
        label: "🟡 Normal Progress"
      - value: "blocked"
        label: "🔴 Blocked"
    default: "normal"

  - key: "include_metrics"
    type: "toggle"
    label: "Include Metrics"
    default: false
    trueLabel: "Add quantitative data"
    falseLabel: "Text only"
---

# {{ week_number }} Weekly Report

**Overall Status**: {{ status }}

## ✅ Accomplishments

{{ accomplishments }}

{{#if in_progress}}
## 🔄 In Progress

{{ in_progress }}
{{/if}}

{{#if blockers}}
## ⚠️ Blockers

{{ blockers }}
{{/if}}

{{#if next_week}}
## 📋 Next Week Plan

{{ next_week }}
{{/if}}

{{#if include_metrics}}
## 📊 Metrics

Please add relevant quantitative metrics.
{{/if}}
```

### 4.2 Code Review Assistant

```yaml
---
title: "Code Review Assistant"
description: "AI-assisted code review to find potential issues"
version: "1.0.0"

inputs:
  - key: "code"
    type: "longText"
    label: "Code to Review"
    placeholder: "Paste code to review..."
    required: true
    rows: 15

  - key: "language"
    type: "select"
    label: "Programming Language"
    options:
      - "Swift"
      - "Python"
      - "JavaScript"
      - "TypeScript"
      - "Go"
      - "Rust"
      - "Java"
      - "Other"
    default: "Swift"

  - key: "focus_areas"
    type: "select"
    label: "Focus Areas"
    multiple: true
    options:
      - value: "security"
        label: "🔒 Security"
      - value: "performance"
        label: "⚡ Performance"
      - value: "readability"
        label: "📖 Readability"
      - value: "best_practices"
        label: "✨ Best Practices"

  - key: "strict_mode"
    type: "toggle"
    label: "Strict Mode"
    default: false
    trueLabel: "Strict review (include suggestions)"
    falseLabel: "Standard review (critical issues only)"
---

Please review the following {{ language }} code:

```{{ language | lowercase }}
{{ code }}
```

{{#if focus_areas}}
**Focus Areas**:
{{#each focus_areas}}
- {{ this }}
{{/each}}
{{/if}}

{{#if strict_mode}}
Please perform a strict review including:
1. Critical issues (must fix)
2. Suggested improvements (recommended)
3. Code style (formatting)
{{else}}
Please focus on:
1. Potential bugs or errors
2. Security vulnerabilities
3. Obvious performance issues
{{/if}}

Please output the review report in Markdown format.
```

---

## 5. Compatibility with Existing Specifications

### 5.1 Comparison Table

| Feature | This Spec | Dotprompt | LangChain | Fabric |
|---------|:---------:|:---------:|:---------:|:------:|
| Front-matter | ✅ | ✅ | ❌ | ❌ |
| UI Control Definition | ✅ | ❌ | ❌ | ❌ |
| Conditional Rendering | ✅ | ✅ | ❌ | ❌ |
| Loop Rendering | ✅ | ✅ | ❌ | ❌ |
| Variable Validation | ✅ | ✅ | ❌ | ❌ |
| Markdown Content | ✅ | ✅ | ❌ | ✅ |

### 5.2 Importing from Other Formats

**From LangChain YAML**:

```yaml
# LangChain
_type: prompt
input_variables: ["topic"]
template: "Tell me about {topic}"

# Converted to this format
---
title: "Topic Generator"
inputs:
  - key: "topic"
    type: "text"
---
Tell me about {{ topic }}
```

**From Fabric Pattern**:

```markdown
# Fabric (system.md)
# IDENTITY
You are an expert...

# STEPS
1. Analyze input
2. Generate output

# Converted to this format
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

## 6. JSON Schema

Complete JSON Schema definition:

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

## 7. References

- [Google Dotprompt](https://genkit.dev/docs/dotprompt/)
- [Microsoft Semantic Kernel](https://learn.microsoft.com/en-us/semantic-kernel/concepts/prompts/)
- [LangChain Prompts](https://python.langchain.com/docs/concepts/prompt_templates/)
- [Anthropic MCP](https://modelcontextprotocol.io/docs/concepts/prompts)
- [Fabric Patterns](https://github.com/danielmiessler/fabric)
