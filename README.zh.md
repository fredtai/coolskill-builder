<div align="center">

# 🔨 CoolSkill Builder

**零依赖 Skill 锻造器 —— 将任何资源转化为跨平台 Agent 技能**

[![GitHub Stars](https://img.shields.io/github/stars/fredtai/coolskill-builder?style=social)](https://github.com/fredtai/coolskill-builder/stargazers)
[![GitHub Forks](https://img.shields.io/github/forks/fredtai/coolskill-builder?style=social)](https://github.com/fredtai/coolskill-builder/network/members)
[![Version](https://img.shields.io/badge/version-1.0.0-blue.svg?cacheSeconds=2592000)](https://github.com/fredtai/coolskill-builder/releases)
[![Zero Dependency](https://img.shields.io/badge/dependencies-zero-brightgreen.svg)](./references/spec.md)
[![Python](https://img.shields.io/badge/python-3.8%2B-blue.svg)](https://python.org)
[![License](https://img.shields.io/badge/license-MIT-yellow.svg)](#license)
[![ClawHub](https://img.shields.io/badge/ClawHub-已发布-purple.svg)](https://clawhub.ai/fredtai/coolskill-builder)

[English](README.md) · [中文](README.zh.md)

</div>

---

## ✨ CoolSkill Builder 是什么？

**CoolSkill Builder** 是 Agent 技能生态的治理节点。它能将任何资源 —— GitHub 仓库、API 文档、自然语言描述、代码片段 —— 转化为**零依赖**、**极致省 Token**、**跨平台**的 Agent 标准化技能。

> 🎯 **一个输入，四个文件，全平台通用。** 放入你的想法，获得生产级 Skill，直接运行在 Kimi、OpenAI、Claude 或任何自研 Agent 上 —— 自带版本隔离和军工级安全校验。

### 为什么 Agent 需要这个？

Agent 生态正在爆发，但每个平台讲不同的方言：
- OpenAI 要 `functions`，带 `strict: true` schema
- Claude 要 `tools`，带 `input_schema`
- 自研 Agent 需要 HTTP 接口
- 而且没人统一版本管理

**CoolSkill Builder** 终结这种混乱。一份规范 → 一套实现 → 三个平台适配器。零锁定、零依赖、零摩擦。

---

## 🚀 快速开始

### 从 GitHub 安装

```bash
git clone https://github.com/fredtai/coolskill-builder.git
cd coolskill-builder
```

### 从 ClawHub 安装

```bash
clawhub install coolskill-builder
# 或
npx clawhub@latest install coolskill-builder
```

### 将代码片段转化为 Skill

**输入：** 粘贴你的 Python 函数

```python
def slugify(text):
    """Convert text to URL-friendly slug."""
    import re
    text = text.lower().strip()
    text = re.sub(r'[^\w\s-]', '', text)
    text = re.sub(r'[\s_-]+', '-', text)
    return text
```

**输出：** 4 个标准化文件

| 文件 | 用途 | 规范 |
|------|------|------|
| `skill.yaml` | 元数据与 schema | 压缩键名、Semver、全局唯一 ID |
| `impl.py` | 纯逻辑实现 | `run(a)` → `{'s','d','e'}`，仅标准库 |
| `test.py` | 隔离测试 | 5 层覆盖：正常/异常/边界/错误/性能 |
| `manifest.json` | 跨平台适配 | OpenAI / Claude / HTTP / 通用格式 |

---

## 🏗️ 架构流程

```
┌─────────────────────────────────────────────────────────────────┐
│                        输入（任意资源）                          │
│  GitHub 仓库 │ API 文档 │ 自然语言描述 │ 代码片段                  │
└────────────────────────────┬────────────────────────────────────┘
                             │
                    ┌────────▼────────┐
                    │  Step 1: 解析    │  提取意图、schema、
                    │    资源          │  边界规则、domain
                    └────────┬────────┘
                             │
                    ┌────────▼────────┐
                    │  Step 2: 生成   │  skill.yaml + impl.py +
                    │    4 文件        │  test.py + manifest.json
                    └────────┬────────┘
                             │
                    ┌────────▼────────┐
                    │  Step 3: 5 层   │  L1: 依赖扫描
                    │  安全校验        │  L2: 注入检测
                    │  （任一失败=     │  L3: 密钥检测
                    │   阻断）         │  L4: 网络边界
                    └────────┬────────┘  L5: 信息泄露防护
                             │
                    ┌────────▼────────┐
                    │  Step 4: 原生   │  隔离进程中运行
                    │    测试          │  失败即阻断
                    └────────┬────────┘
                             │
                    ┌────────▼────────┐
                    │  Step 5: 版本   │  registry/{id}/{version}/
                    │    隔离注册      │  自动 Semver 递增
                    └────────┬────────┘  历史版本只读保留
                             │
                    ┌────────▼────────┐
                    │  Step 6: GitHub │  推送仓库
                    │   + ClawHub      │  + clawhub publish
                    └─────────────────┘
```

---

## 🔒 5 层安全校验

| 层级 | 检查项 | 规则 |
|------|--------|------|
| **L1** | 依赖扫描 | 仅标准库白名单 —— 禁止任何第三方库 |
| **L2** | 注入防护 | 阻断 `eval`, `exec`, `os.system`, `subprocess`, `pty` |
| **L3** | 密钥检测 | 禁止硬编码 API Key、Token、密码 |
| **L4** | 网络边界 | 未声明 `perms: [net]` 则禁止网络调用 |
| **L5** | 泄露防护 | 错误信息禁止包含 traceback、os.environ 或敏感数据 |

> ⚠️ **铁律：** 任一层失败 = **全面阻断**。不写入 Registry，不推送 GitHub，没有例外。

---

## 🌐 跨平台兼容

CoolSkill Builder 生成的技能可在任意平台运行：

### Kimi / 通用 Agent
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

## 📦 版本隔离机制

每个技能拥有独立命名空间，永久隔离。

```
registry/
├── index.json                          # 全局元数据索引
└── {domain}-{func}-{rand3}/            # 例: text-slugify-a7k
    ├── v1.0.0/                         # 不可变归档
    ├── v1.0.1/                         # 当前最新
    └── latest -> v1.0.1                # 符号链接
```

| 场景 | 版本变更 |
|------|----------|
| 首次创建 | `1.0.0` |
| Bug 修复 / 迭代 | `1.0.1` (Patch +1) |
| 功能重大变更 | `1.1.0` (Minor +1) |
| 破坏性重构 | `2.0.0` (Major +1) |

---

## 🛠️ 内置工具链

| 脚本 | 用途 |
|------|------|
| `scripts/security_scan.py` | L1-L5 安全扫描器 |
| `scripts/generate_skill_id.py` | 全局唯一 Skill ID 生成器 |
| `scripts/validate_impl.py` | 语法检查 + 接口校验 + Token 密度分析 |

---

## 📚 文档

| 文档 | 内容 |
|------|------|
| [references/spec.md](references/spec.md) | 完整规格：零依赖、省Token、版本隔离 |
| [references/file-templates.md](references/file-templates.md) | 4 文件模板 + 跨平台调用示例 |
| [references/security-rules.md](references/security-rules.md) | 5 层安全校验规则 |
| [references/registry-format.md](references/registry-format.md) | Registry 目录结构 + 索引格式 |

---

## 🤝 参与贡献

我们欢迎各种形式的贡献：
- 🐛 Bug 报告
- 💡 功能建议
- 📖 文档改进
- 🔧 新平台适配器

提交 PR 前请先阅读 [Contributing Guide](CONTRIBUTING.md)。

---

## 👤 作者

**Fred Tai** ([@fredtai](https://github.com/fredtai))

> 🧠 由相信 Agent 工具应该**简单、安全、通用**的开发者打造。

---

## 📜 许可证

[MIT](LICENSE) © Fred Tai
