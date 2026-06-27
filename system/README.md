# AI Coding 系统
> 一套完整的个人 AI 编码方法论，两条路径，14 个文件，替代所有外部框架。

---

## 系统结构

```
project-root/
│
├── CLAUDE.md                          ← 主入口（始终加载，始终生效）
│
└── .claude/
    ├── settings.json                  ← Hooks（自动跑测试、完成前检查）
    │
    ├── skills/                        ← 工作流技能（按需调用）
    │   ├── clarify/SKILL.md           ← 需求澄清
    │   ├── spec/SKILL.md              ← 设计规格文档
    │   ├── plan/SKILL.md              ← 任务拆解
    │   ├── execute/SKILL.md           ← 实现（含懒惰阶梯 + TDD）
    │   ├── review/SKILL.md            ← 代码审查
    │   ├── debug/SKILL.md             ← 结构化调试
    │   └── handoff/SKILL.md           ← Session 交接与续接
    │
    ├── agents/                        ← 专业 Subagent（隔离型任务）
    │   ├── researcher.md              ← 研究型（文档/方案调研）
    │   └── reviewer.md                ← 审查型（独立 review 视角）
    │
    └── templates/                     ← 文档模板
        ├── spec.md                    ← 设计规格模板
        ├── task-list.md               ← 任务列表模板
        └── handoff.md                 ← 交接文档模板
```

---

## 两条路径

### 🟢 简单路径（≤3 文件，需求清晰，≤30 分钟）

```
需求清晰 → execute
需求模糊 → clarify → execute
遇到 bug → debug → execute
```

### 🔵 复杂路径（≥4 文件，或有架构/安全影响）

```
clarify → spec → plan → execute（逐任务）→ review
                                              ↓
                                         有问题 → execute（修复）→ review
                                              ↓
                                         通过 → 完成
```

**路径选择由 CLAUDE.md 自动判断，或询问用户确认。**

---

## 快速安装

```bash
# 复制到你的项目
cp -r .claude /your-project/
cp CLAUDE.md /your-project/

# 填写项目信息（技术栈、命令、Gotcha）
# 编辑 CLAUDE.md 底部的"项目信息"部分
```

---

## 每个文件的作用

| 文件 | 始终加载？ | 作用 |
|------|-----------|------|
| `CLAUDE.md` | ✅ 是 | 行为原则 + 路径路由 + 项目信息 |
| `settings.json` | ✅ 是 | 自动跑测试、完成前检查、危险命令拦截 |
| `skills/clarify` | 按需 | 把模糊需求变成清晰意图声明 |
| `skills/spec` | 按需 | 复杂任务前生成设计规格文档 |
| `skills/plan` | 按需 | 把 spec 拆成有序任务列表 |
| `skills/execute` | 按需 | 实现代码（懒惰阶梯 + TDD + 自检） |
| `skills/review` | 按需 | 三视角代码审查（质量/安全/可维护性） |
| `skills/debug` | 按需 | 结构化调试（6 步协议，找根因） |
| `skills/handoff` | 按需 | Session 交接与续接 |
| `agents/researcher` | 按需 | 隔离调研，只返回摘要 |
| `agents/reviewer` | 按需 | 独立 review subagent |
| `templates/spec` | 按需 | 设计规格文档模板 |
| `templates/task-list` | 按需 | 任务列表模板 |
| `templates/handoff` | 按需 | Session 交接文档模板 |

---

## 使用示例

### 示例 1：简单任务（加一个字段验证）

```
用户：给 user 表加个 email 格式验证

Claude 判断：3 文件以内，需求清晰，简单路径
→ 读取 execute skill
→ 开始前：确认范围 + 验证方式
→ 懒惰阶梯：标准库能做（zod/yup），不自己写正则
→ TDD：先写失败测试，再实现
→ 完成后自检 → 输出完成报告
```

### 示例 2：复杂任务（设计一个权限系统）

```
用户：我想加一个 RBAC 权限系统

Claude 判断：多文件，涉及 Auth 和架构，复杂路径

Step 1: clarify skill
→ 问：是给哪类用户？角色是固定的还是可配置的？
→ 产出：意图声明

Step 2: spec skill
→ 分段呈现设计：数据模型 → 中间件 → API → 测试
→ 每段等确认 → 产出：docs/specs/2026-06-27-rbac.md

Step 3: plan skill
→ 拆分：T01 数据模型 → T02 中间件 → T03 API → T04 前端 → T05 测试
→ 标注依赖关系

Step 4: execute（逐任务）
→ 每个任务独立完成，有独立验收

Step 5: review skill
→ 安全视角重点检查（Auth 相关）
→ 输出 review 报告 → 用户确认 → 完成
```

### 示例 3：调试

```
用户：登录之后跳转到了错误的页面

Claude 判断：需要调试，debug skill

→ Step 1 复现：能稳定复现吗？
→ Step 2 最小化：写最小复现用例
→ Step 3 假设：3 个可能原因
→ Step 4 插桩：验证假设
→ Step 5 修复：针对根因
→ Step 6 回归：加测试，确认其他登录场景正常
```

---

## 维护建议

### 高频维护（每次使用中）
- 遇到新 Gotcha → 立刻加到 CLAUDE.md
- 完成一个任务 → 更新任务列表里的状态
- Session 快结束 → 用 handoff skill 生成交接文档

### 低频维护（每月）
- 清理 CLAUDE.md 里过时的规则（保持 ≤150 行）
- 检查 skills 是否需要更新
- 把 docs/handoff/ 里超过 30 天的文档归档

### 跨项目复用
- `.claude/` 目录可以直接复制到新项目
- 每个项目只需要更新 `CLAUDE.md` 底部的"项目信息"
- skills 和 agents 是通用的，不需要修改

---

## 设计原则（这套系统背后的逻辑）

1. **CLAUDE.md 轻，Skills 重**：始终加载的只放行为原则和路由，细节放到按需加载的 Skills 里

2. **两条路径，一套原则**：简单任务不走完整流程，但所有任务都遵守相同的代码质量规则

3. **每一步都有输出物**：clarify→意图声明，spec→文档，plan→任务列表，handoff→交接文档。输出物让进度可见，让中断可恢复

4. **验证是流程的一部分，不是可选步骤**：PostToolUse hook 和 Stop hook 把验证变成了不可跳过的行为

5. **知识固化优先于重复学习**：Gotcha 写进 CLAUDE.md，交接文档写进 handoff，让每次 session 积累而不是重置

---

## 来源与致谢

这套系统综合了以下框架的核心思想：
- **Karpathy CLAUDE.md**：四条行为原则
- **ponytail**：懒惰阶梯，shortcut 注释系统
- **superpowers**：TDD 门控，subagent 隔离，skill 自动触发
- **GSD**：phase 隔离，context 管理，HANDOFF 续接
- **addyosmani/agent-skills**：source-driven，三视角 review
- **mattpocock/skills**：单职责 skill 设计，工作流强制理念
- **OpenSpec**：范围边界（out of scope），delta 增量文档
- **BMAD**：两阶段分离（规划/执行），story 文件作为通信协议
- **ralph**：自主循环，交接机制，circuit breaker 概念
- **ECC**：AgentShield 安全意识，hook 配置标准
