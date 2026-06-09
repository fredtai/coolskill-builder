# CoolSkill Builder - 同步指南

## 1. GitHub 同步

### 1.1 获取 Personal Access Token

1. 访问 https://github.com/settings/tokens
2. 点击 "Generate new token (classic)"
3. 勾选权限：`repo` (完整仓库控制)
4. 生成并复制 Token

### 1.2 环境变量配置

```bash
export GITHUB_TOKEN="ghp_xxxxxxxxxxxxxxxxxxxx"
export GITHUB_USERNAME="your-github-username"
```

### 1.3 自动同步脚本

```bash
# 进入 skill 目录
cd /mnt/agents/output/coolskill-builder

# 运行同步脚本（零依赖，仅使用 Python 标准库）
python3 scripts/github_sync.py
```

或者手动执行：

```bash
# 创建新仓库
curl -X POST https://api.github.com/user/repos \
  -H "Authorization: token $GITHUB_TOKEN" \
  -H "Accept: application/vnd.github.v3+json" \
  -d '{"name":"coolskill-builder","description":"Skill generator - zero-dependency, cross-platform, version-isolated skill builder","private":false}'

# 初始化并推送
cd /mnt/agents/output/coolskill-builder
git init
git add .
git commit -m "feat: initial release of coolskill-builder v1.0.0"
git branch -M main
git remote add origin https://$GITHUB_USERNAME:$GITHUB_TOKEN@github.com/$GITHUB_USERNAME/coolskill-builder.git
git push -u origin main
```

## 2. ClawHub 同步

ClawHub 是 OpenClaw 的公共技能注册表（https://clawhub.ai）。

### 2.1 前提条件

- 安装 OpenClaw CLI: `npm install -g openclaw`
- 配置 ClawHub 账号

### 2.2 发布命令

```bash
# 发布到 ClawHub
openclaw skill publish /mnt/agents/output/coolskill-builder

# 或直接使用 clawhub CLI
npx clawhub@latest publish coolskill-builder
```

### 2.3 验证发布

```bash
# 搜索你的 skill
openclaw skill search coolskill-builder

# 查看详情
openclaw skill info coolskill-builder
```

## 3. 文件清单

推送至 GitHub / ClawHub 的文件：

```
coolskill-builder/
├── SKILL.md                              # 核心技能文件
├── scripts/
│   ├── security_scan.py                  # L1-L5 安全扫描器
│   ├── generate_skill_id.py              # Skill ID 生成器
│   └── validate_impl.py                  # impl.py 校验器
├── references/
│   ├── spec.md                           # 完整规格说明
│   ├── file-templates.md                 # 4 文件模板
│   ├── security-rules.md                 # 安全校验规则
│   └── registry-format.md              # Registry 格式规范
└── SYNC_GUIDE.md                         # 本文件
```

## 4. 发布状态检查清单

- [ ] GitHub 仓库已创建
- [ ] 所有文件已推送至 main 分支
- [ ] ClawHub 上可搜索到 skill
- [ ] skill 安装测试通过

## 5. 版本迭代

下次更新时，Skill 会自动递增版本号：

```
v1.0.0 → v1.0.1 (修复)
v1.0.1 → v1.1.0 (功能变更)
v1.1.0 → v2.0.0 (破坏性重构)
```
