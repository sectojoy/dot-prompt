---
title: "Article Translation"
description: "Maintain the structure of the article and translate it into another language."
version: "1.0.0"

inputs:
  - key: "language"
    type: "select"
    label: "Choose Language"
    options:
      - "English"
      - "Chinese (中文)"
      - "Spanish (Español)"
      - "French (Français)"
      - "German (Deutsch)"
      - "Japanese (日本語)"
      - "Korean (한국어)"
      - "Portuguese (Português)"
      - "Russian (Русский)"
      - "Italian (Italiano)"
      - "Dutch (Nederlands)"
      - "Arabic (العربية)"
      - "Hindi (हिन्दी)"
      - "Turkish (Türkçe)"
    default: "English"
    required: true
  - key: "text"
    type: "longText"
    label: "Article"
    placeholder: "Markdown Article content"
    required: true
---

<instruction>
  1. Please read the input variables, where:
     - {{language}} indicates the target translation language (e.g., English, Spanish, Chinese, etc.).
     - {{content}} represents the content of the Markdown document to be translated.
  2. Translate the input Markdown document content into {{language}}. You must strictly preserve the Markdown format, ensuring that all headers, lists, code blocks, links, and other Markdown syntax remain correct and intact.
  3. During translation, convert only the text content. All Markdown markers (e.g., #, -, ```, etc.) must remain exactly the same in the translated output.
  4. The output must not contain any XML tags; output only the translated pure Markdown document.
</instruction>

<input>
  <variable name="language" type="string">
    The target translation language, e.g., "English", "Spanish", etc.
  </variable>
  <variable name="content" type="markdown">
    The Markdown document content to be translated.
  </variable>
</input>

<example>
  Example:
  Input:
    language: Spanish
    content: |
      # Title
      This is example text, which contains **bold** and *italic* formatting.
  
  Output:
      # Título
      Este es un texto de ejemplo, que contiene formato **negrita** y *cursiva*.
</example>

<output>
  The output should be text containing only the translated Markdown document content, without any other tags or explanations.
</output>
