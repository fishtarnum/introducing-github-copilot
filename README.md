#!/usr/bin/env bash
set -e
REPO="https://github.com/fishtarnum/introducing-github-copilot.git"
DIR="introducing-github-copilot-temp"

rm -rf "$DIR"
git clone "$REPO" "$DIR"
cd "$DIR"

# Create files
cat > README.md <<'EOF'
# Introducing GitHub Copilot

This repository is a short introduction and hands-on guide to GitHub Copilot — what it is, how to get started, and examples for using Copilot in real coding workflows.

## 简介 (Chinese / 中文)

GitHub Copilot 是由 OpenAI 驱动的编程助理，可以在你编写代码时提供自动补全、生成函数体、重构建议和注释。它能与常用编辑器（例如 VS Code）集成，帮助开发者更快地完成常见任务。

## What you'll find here

- README (this file) — bilingual overview and quickstart
- docs/usage.md — detailed usage guide and prompts (中文)
- examples/ — small Node.js and Python examples demonstrating Copilot-assisted code
- CONTRIBUTING.md — how to contribute
- LICENSE — MIT

## Quick start / 快速开始

1. Install the GitHub Copilot extension for VS Code:
   - Open VS Code -> Extensions -> search "GitHub Copilot" -> Install
2. Sign in to GitHub in VS Code and enable Copilot (follow extension prompts)
3. Open one of the example files in this repo and start typing. Try the sample prompt below.

### Sample prompts (try these in a comment or new file):

- "Write a function that fetches JSON from a URL and retries on failure."
- "Create a CLI that reads a CSV and prints summary statistics."
- "Add unit tests for the following function..."

——

Repository: https://github.com/fishtarnum/introducing-github-copilot
EOF

mkdir -p docs examples/node examples/python .github/workflows

cat > docs/usage.md <<'EOF'
# GitHub Copilot 使用指南（中文）

本文件提供实用提示、prompt 模板与最佳实践，帮助你更高效地和 Copilot 协作。

## 快速提示（prompts）

- 代码生成类：
  - "实现一个函数，根据传入的数组返回去重后的数组并保留顺序。"
  - "为以下函数编写单元测试：<粘贴函数代码>。使用 pytest。"

- 文档/注释：
  - 在函数上方写一段英文 docstring，包含参数、返回值和示例。"

- 重构与优化：
  - "重构以下代码以提高可读性并减少重复：<粘贴代码>。"

## 与 Copilot 协作的最佳实践

1. 提供足够上下文：在文件顶部或函数周围添加注释/类型签名，Copilot 会基于这些提示生成更合适的代码。
2. 小步迭代：先写函数签名和注释，再让 Copilot 补全实现；多次接受/拒绝建议以引导生成方向。
3. 审查生成代码：自动生成内容可能包含错误或安全问题——总是进行代码审查、单元测试与静态分析。
4. 隐私与敏感数据：避免在代码中写入真实的密钥、密码或敏感数据；在使用 Copilot 时不要粘贴受限数据作为 prompt。

## 常见提示模板

- 生成带类型提示的函数：
  "# 请实现下面的函数，使用 Python 类型注解：\n def summarize(texts: List[str]) -> Dict[str, int]:"

- 生成可执行示例：
  "生成一个可以直接运行的 Node.js 示例，演示如何使用 fetch 从 API 拉取数据并打印结果。"

## 隐私与合规

- Copilot 可能会基于公共训练数据生成代码片段；在企业或受监管环境中使用前，请核查合规要求。

---

如果你想让我为某个语言/框架撰写更多示例（例如 React、Django、Go），告诉我你希望覆盖的内容。
EOF

cat > examples/node/hello.js <<'EOF'
// Node.js 示例：hello.js
// 这是一个简单的示例脚本，演示如何用 fetch 请求公开 API（Node 18+ 内置 fetch）

async function fetchJson(url) {
  try {
    const res = await fetch(url);
    if (!res.ok) throw new Error(`HTTP ${res.status}`);
    return await res.json();
  } catch (err) {
    console.error('Request failed:', err.message);
    throw err;
  }
}

async function main() {
  const url = 'https://api.github.com/repos/octocat/hello-world';
  const data = await fetchJson(url);
  console.log('Repository:', data.full_name);
}

main().catch(() => process.exit(1));
EOF

cat > examples/python/quickstart.py <<'EOF'
# Python 示例：quickstart.py
# 演示：从 JSON API 拉取数据并打印部分字段

import sys
import urllib.request
import json


def fetch_json(url: str) -> dict:
    with urllib.request.urlopen(url) as r:
        return json.load(r)


def main():
    url = 'https://api.github.com/repos/octocat/hello-world'
    data = fetch_json(url)
    print('Repository:', data.get('full_name'))


if __name__ == '__main__':
    try:
        main()
    except Exception as e:
        print('Error:', e, file=sys.stderr)
        sys.exit(1)
EOF

cat > .gitignore <<'EOF'
# Node.js
node_modules/

# Logs
logs
*.log
npm-debug.log*
yarn-debug.log*
yarn-error.log*

# Runtime data
pids
*.pid
*.seed

# Build
dist/

# Environment
.env

# macOS
.DS_Store
EOF

cat > LICENSE <<'EOF'
MIT License

Copyright (c) 2026 fishtarnum

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
EOF

cat > CONTRIBUTING.md <<'EOF'
# Contributing

Thanks for considering contributing! You can help by:

- Submitting improvements to the docs in docs/usage.md (prompts, best practices)
- Adding more language/framework examples under examples/
- Opening issues to report typos or suggest new content

Please follow a simple workflow: fork -> branch -> pull request. Describe your change and why it's helpful.
EOF

cat > .github/workflows/ci.yml <<'EOF'
name: CI
on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Node version
        run: node --version
      - name: Python version
        run: python3 --version
      - name: Quick smoke run for Node example
        run: |
          node --version
          if node -e "process.exit(0)"; then echo "Node present"; fi
      - name: Quick smoke run for Python example
        run: |
          python3 -c "import sys; print('py ok')"
EOF

# Commit and push
git add .
git commit -m "Initialize repository with README, docs, examples, LICENSE, .gitignore, CONTRIBUTING, CI"
git push origin main

echo "Done — files created and pushed to main."
