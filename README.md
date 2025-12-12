dot-prompt Specification (.prompt)An open standard for storing, sharing, and managing AI prompts as file-based templates.一个基于文件的、开放的 AI 提示词模版存储标准。📌 Introduction (简介)The .prompt format is a lightweight, Markdown-compatible file format designed to separate AI prompts from application code. It treats prompts as documents, allowing for version control, collaboration, and easy editing..prompt 格式是一种轻量级的、兼容 Markdown 的文件格式，旨在将 AI 提示词与应用程序代码分离。它将提示词视为文档，从而支持版本控制、团队协作和轻松编辑。📄 File Extension (文件后缀)The official file extension is .prompt.It is recommended to associate this extension with text/markdown for syntax highlighting in editors.官方文件后缀为 .prompt。📝 Syntax Structure (语法结构)A .prompt file consists of two parts:Metadata (Frontmatter): YAML-based configuration (optional but recommended).Body: The prompt template content (Markdown + Mustache variables).一个 .prompt 文件由两部分组成：元数据：基于 YAML 的配置信息（可选，但推荐）。正文：提示词模版内容（Markdown + Mustache 变量）。Example (示例)---
title: "Code Refactor Expert"
description: "Refactors code for better readability and performance."
model: "gpt-4"
tags: [coding, refactoring, python]
version: "1.0"
---

You are an expert software engineer specializing in {{language}}.
Your task is to refactor the following code to improve its **readability** and **performance**.

### Rules:
- Do not change the logic.
- Add comments where necessary.
- Use {{style}} coding style.

### Code:
```{{language}}
{{input_code}}

## 🛠 Features (特性)

* **Frontmatter**: Store metadata like `title`, `author`, `default_model`, `parameters`.
* **Variables**: Use `{{variable}}` syntax for dynamic inputs.
* **Markdown**: Full support for standard Markdown formatting.

## 🔌 Integration (集成指南)

### iOS (Info.plist)

To open `.prompt` files in your iOS app, register the following UTI:

```xml
<key>UTExportedTypeDeclarations</key>
<array>
    <dict>
        <key>UTTypeIdentifier</key>
        <string>com.yourcompany.dotprompt</string>
        <key>UTTypeDescription</key>
        <string>Prompt Template</string>
        <key>UTTypeConformsTo</key>
        <array>
            <string>public.plain-text</string>
            <string>net.daringfireball.markdown</string>
        </array>
        <key>UTTypeTagSpecification</key>
        <dict>
            <key>public.filename-extension</key>
            <array>
                <string>prompt</string>
            </array>
        </dict>
    </dict>
</array>
🤝 ContributingWe welcome contributions to the specification! Please modify SPEC.md and submit a Pull Request.
### 此外，这个 Git 仓库里还应该放什么？

除了 `README.md`，为了让它像一个正经的“规范项目”，你建议建立以下文件夹结构：

```text
dot-prompt/
├── README.md          # 项目主页
├── SPEC.md            # 详细的技术规范文档（定义解析规则、变量语法）
├── examples/          # 存放各种 .prompt 文件的示例
│   ├── coding.prompt
│   ├── writing.prompt
│   └── translation.prompt
├── icons/             # 存放你设计的 .prompt 文件图标（svg/png）
├── grammar/           # (进阶) 存放 VSCode 的语法高亮插件配置
└── schemas/           # (进阶) 如果以后支持 JSON 验证，放 schema 文件
