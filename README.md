<div align="center">

# 🔨 CoolSkill Builder

**The Zero-Dependency Skill Forge — Turn Any Resource into a Cross-Platform Agent Skill**

[![GitHub Stars](https://img.shields.io/github/stars/fredtai/coolskill-builder?style=social)](https://github.com/fredtai/coolskill-builder/stargazers)
[![GitHub Forks](https://img.shields.io/github/forks/fredtai/coolskill-builder?style=social)](https://github.com/fredtai/coolskill-builder/network/members)
[![Version](https://img.shields.io/badge/version-1.0.0-blue.svg?cacheSeconds=2592000)](https://github.com/fredtai/coolskill-builder/releases)
[![Zero Dependency](https://img.shields.io/badge/dependencies-zero-brightgreen.svg)](./references/spec.md)
[![Python](https://img.shields.io/badge/python-3.8%2B-blue.svg)](https://python.org)
[![License](https://img.shields.io/badge/license-MIT-yellow.svg)](#license)
[![ClawHub](https://img.shields.io/badge/ClawHub-published-purple.svg)](https://clawhub.ai/fredtai/coolskill-builder)

[English](README.md) · [中文](README.zh.md)

</div>

---

## ✨ What is CoolSkill Builder?

**CoolSkill Builder** is a governance node for the Agent skill ecosystem. It transforms any resource — GitHub repos, API docs, natural language descriptions, code snippets — into **zero-dependency**, **token-optimized**, **cross-platform** Agent skills.

> 🎯 **One input, four files, any platform.** Drop in your idea, get back a production-ready skill that runs on Kimi, OpenAI, Claude, or any custom Agent — with version isolation and military-grade security validation.

### Why Agents Need This

The Agent ecosystem is exploding. But every platform speaks a different dialect:
- OpenAI wants `functions` with `strict: true` schemas
- Claude expects `tools` with `input_schema`
- Custom agents need HTTP endpoints
- And nobody agrees on how to version anything

**CoolSkill Builder** cuts through the noise. One spec → one implementation → three platform adapters. Zero lock-in, zero dependencies, zero friction.

---

## 🚀 Quick Start

### Install from GitHub

```bash
git clone https://github.com/fredtai/coolskill-builder.git
cd coolskill-builder
```

### Install from ClawHub

```bash
clawhub install coolskill-builder
# or
npx clawhub@latest install coolskill-builder
```

### Turn a Code Snippet into a Skill

**Input:** Paste your Python function

```python
def slugify(text):
    """Convert text to URL-friendly slug."""
    import re
    text = text.lower().strip()
    text = re.sub(r'[^\w\s-]', '', text)
    text = re.sub(r'[\s_-]+', '-', text)
    return text
```

**Output:** 4 standardized files

| File | Purpose | Spec |
|------|---------|------|
| `skill.yaml` | Metadata & schemas | Compressed keys, Semver, unique ID |
| `impl.py` | Pure logic | `run(a)` → `{'s','d','e'}`, stdlib only |
| `test.py` | Isolated tests | 5-layer coverage: normal/edge/error/boundary/perf |
| `manifest.json` | Cross-platform adapters | OpenAI / Claude / HTTP / Universal |

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        INPUT (Any Resource)                      │
│  GitHub Repo │ API Docs │ Natural Language │ Code Snippet        │
└────────────────────────────┬────────────────────────────────────┘
                             │
                    ┌────────▼────────┐
                    │  Step 1: Parse  │  Extract intent, schema,
                    │   Resource      │  boundaries, domain
                    └────────┬────────┘
                             │
                    ┌────────▼────────┐
                    │  Step 2: Gen 4  │  skill.yaml + impl.py +
                    │    Files        │  test.py + manifest.json
                    └────────┬────────┘
                             │
                    ┌────────▼────────┐
                    │  Step 3: 5-Layer│  L1: Dependency scan
                    │  Security Check │  L2: Injection detection
                    │  (Any fail =    │  L3: Secret detection
                    │   BLOCK)        │  L4: Network boundary
                    └────────┬────────┘  L5: Info leak guard
                             │
                    ┌────────▼────────┐
                    │  Step 4: Native │  Run in isolated process
                    │     Test        │  BLOCK on any failure
                    └────────┬────────┘
                             │
                    ┌────────▼────────┐
                    │  Step 5: Version│  registry/{id}/{version}/
                    │   Registry      │  Semver auto-increment
                    └────────┬────────┘  Read-only history
                             │
                    ┌────────▼────────┐
                    │  Step 6: GitHub │  Push to repo
                    │    + ClawHub    │  + clawhub publish
                    └─────────────────┘
```

---

## 🔒 5-Layer Security

| Layer | Check | Rule |
|-------|-------|------|
| **L1** | Dependency Scan | Stdlib whitelist only — no third-party imports |
| **L2** | Injection Guard | Block `eval`, `exec`, `os.system`, `subprocess`, `pty` |
| **L3** | Secret Detection | No hardcoded API keys, tokens, passwords |
| **L4** | Network Boundary | No network calls without `perms: [net]` declaration |
| **L5** | Leak Prevention | No `traceback`, `os.environ`, or sensitive data in errors |

> ⚠️ **Hard rule:** Any layer fails = **full block**. No registry write, no GitHub push, no exceptions.

---

## 🌐 Cross-Platform Compatibility

CoolSkill Builder generates skills that work everywhere:

### Kimi / Universal Agent
```python
import sys
sys.path.insert(0, 'registry/{skill-id}/{version}')
from impl import run
result = run({'x': 'input'})
# → {'s': 'ok', 'd': 'result', 'e': None}
```

### OpenAI Function
```json
{
  "name": "skill_name",
  "arguments": "{\"x\": \"input\"}"
}
```

### Claude Tool
```xml
<tool_use>
  <name>skill_name</name>
  <arguments>{"x": "input"}</arguments>
</tool_use>
```

### HTTP API
```bash
curl -X POST http://agent-cluster/skill/{skill-id} \
  -H "Content-Type: application/json" \
  -H "X-Skill-Version: {version}" \
  -d '{"x": "input"}'
```

---

## 📦 Version Isolation

Every skill lives in its own namespace. Forever.

```
registry/
├── index.json                          # Global metadata index
└── {domain}-{func}-{rand3}/            # e.g. text-slugify-a7k
    ├── v1.0.0/                         # Immutable archive
    ├── v1.0.1/                         # Current latest
    └── latest → v1.0.1                 # Symlink
```

| Scenario | Version Bump |
|----------|-------------|
| First creation | `1.0.0` |
| Bug fix / iteration | `1.0.1` (Patch +1) |
| Major feature change | `1.1.0` (Minor +1) |
| Breaking refactor | `2.0.0` (Major +1) |

---

## 🛠️ Built-in Toolchain

| Script | Purpose |
|--------|---------|
| `scripts/security_scan.py` | Automatic L1-L5 security validation |
| `scripts/generate_skill_id.py` | Global unique skill ID generator |
| `scripts/validate_impl.py` | Syntax check + interface verification + token density |

---

## 📚 Documentation

| Document | Content |
|----------|---------|
| [references/spec.md](references/spec.md) | Full spec: zero-dependency, token-efficiency, version isolation |
| [references/file-templates.md](references/file-templates.md) | 4-file templates + cross-platform call examples |
| [references/security-rules.md](references/security-rules.md) | 5-layer security validation rules |
| [references/registry-format.md](references/registry-format.md) | Registry directory structure + index format |

---

## 🤝 Contributing

We welcome contributions! Whether it's:
- 🐛 Bug reports
- 💡 Feature requests
- 📖 Documentation improvements
- 🔧 New platform adapters

Please read our [Contributing Guide](CONTRIBUTING.md) before submitting PRs.

---

## 👤 Author

**Fred Tai** ([@fredtai](https://github.com/fredtai))

> 🧠 Built by developers who believe Agent tools should be **simple, safe, and universal**.

---

## 📜 License

[MIT](LICENSE) © Fred Tai
