---
title: "Markdown Article Translation"
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
  - key: "content"
    type: "longText"
    label: "Article"
    placeholder: "Markdown Article content"
    required: true
---

Instruction: Markdown Article Translation
requirements: 
1. **Translation Goal**: Translate the **input content** into `{{language}}`.
2. **Format Preservation (CRITICAL)**:
* You must **strictly preserve** the original Markdown format.
* Ensure all headers, lists, code blocks, links, bolding/italics, and other Markdown syntax remain correct and intact.
* **Do not** translate code inside code blocks unless it is comment text.
* All Markdown markers (e.g., `#`, `-`, `*`, `````) must remain exactly the same in the output.
3. **Output Constraints**:
* Output **only** the translated pure Markdown document.
* Do not include any XML tags, introductory text, or explanations.

**Here is the input content**
---

{{content}}
