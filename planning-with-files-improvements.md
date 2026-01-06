# Planning-with-Files Skill 优化方案

## Implementation Status
- [x] **Optimization 1 (Update Checkpoints):** Implemented in SKILL.md (Critical Rules).
- [x] **Optimization 2 (File Indexing):** Implemented in SKILL.md (Template & Workflow) and examples.md.
- [x] **Optimization 3 (Answer Tracking):** Implemented in SKILL.md (Template & Workflow) and examples.md.

## 文档说明

本文档总结了 `planning-with-files` skill 在使用过程中发现的三个问题，以及对应的优化方案。可用于修改 skill 源码进行测试。

---

## 问题分析

### 问题 1：对话次数多了，忘记更新 task_plan.md

**现象：**
- 在同一个会话中，当对话次数增多（特别是超过 20-30 次工具调用后），AI 可能会忘记更新 `task_plan.md` 文件
- 导致进度跟踪不准确，新会话时无法准确了解之前的进展

**根本原因：**
- Skill 设计依赖"Read Before Decide"模式，但没有强制性的更新检查机制
- 当上下文变长时，即使读取了 `task_plan.md`，也可能因为注意力分散而忘记更新

**影响：**
- ⚠️ 中等影响：进度跟踪不准确，但可以通过重新读取文件恢复

---

### 问题 2：创建了新 md 文件，但没有建立索引

**现象：**
- 除了 3 个核心文件（`task_plan.md`、`notes.md`、`[deliverable].md`）外，还会创建其他 md 文件
- 这些新文件没有在核心文件中建立索引或引用
- 新会话时，AI 可能不知道这些文件的存在，容易忽略

**根本原因：**
- Skill 设计只关注 3 文件模式，没有文件索引机制
- 缺少"Related Files"或"File Index"的概念

**影响：**
- ⚠️ 高影响：可能导致重要信息丢失，新会话时无法找到相关文件

**示例场景：**
```
创建了以下文件：
- task_plan.md
- notes.md
- implementation_details.md  ← 新文件，但未索引
- api_reference.md          ← 新文件，但未索引
- final_report.md

新会话时，AI 可能只看到 task_plan.md，不知道其他文件的存在
```

---

### 问题 3：Key Questions 只更新状态，不记录答案

**现象：**
- `task_plan.md` 中的 "Key Questions" 部分只列出问题
- 当问题得到回答后，只更新完成状态（如标记 ✅），但不记录答案本身
- 答案散落在对话历史中，难以追溯

**根本原因：**
- 原始模板设计只关注"问题列表"，没有答案字段
- 缺少答案记录机制

**影响：**
- ⚠️ 高影响：关键决策和答案无法追溯，新会话时需要重新查找

**示例场景：**
```markdown
## Key Questions
1. 应该使用哪种架构？ ✅ 已完成
2. 性能优化方案是什么？ ✅ 已完成
```
但看不到具体的答案，需要去翻对话历史或 notes.md

---

## 优化方案

### 优化 1：增强更新检查机制

**方案：**
在 skill 的核心规则中添加更明确的更新检查点：

```markdown
### 6. Update Checkpoints
After EVERY major action (not just phases), check if task_plan.md needs updating:
- [ ] Did I complete a phase? → Update checkbox
- [ ] Did I answer a key question? → Update Key Questions section
- [ ] Did I create a new file? → Add to Related Files section
- [ ] Did I encounter an error? → Add to Errors Encountered section
- [ ] Did I make a decision? → Add to Decisions Made section

**Update Frequency:**
- After each phase completion: MANDATORY
- After every 5-10 tool calls: RECOMMENDED
- Before any major decision: MANDATORY (read + update)
```

**实现位置：**
- 在 `SKILL.md` 的 "Critical Rules" 部分添加
- 在 "The Loop in Detail" 部分强化

---

### 优化 2：添加文件索引机制

**方案：**
在 `task_plan.md` 模板中添加 "Related Files" 部分：

```markdown
## Related Files

This section tracks all markdown files created during this task.

| File | Purpose | Last Updated | Status |
|------|---------|--------------|--------|
| notes.md | Research findings and sources | 2025-01-03 | Active |
| implementation_details.md | Technical implementation notes | 2025-01-03 | Active |
| api_reference.md | API documentation | 2025-01-03 | Reference |
| final_report.md | Final deliverable | 2025-01-04 | Complete |

**File Index Rules:**
- Add new files immediately after creation
- Update "Last Updated" when file is modified
- Mark "Status" as: Active / Reference / Complete / Archived
```

**实现位置：**
- 更新 `task_plan.md` 模板
- 在 skill 的 "Core Workflow" 中添加规则：创建新 md 文件后必须更新索引

---

### 优化 3：Key Questions 添加答案记录

**方案：**
将 "Key Questions" 部分改为 "Key Questions & Answers"，包含答案字段：

```markdown
## Key Questions & Answers

1. **[问题简述]**
   - Status: ⏳ Pending / ✅ Answered / ❌ Dropped / 🔄 In Progress
   - Answer: 
     - [用 1-3 句话总结答案，详细内容可引用 notes.md 或其他文件]
   - Evidence / Reference:
     - [链接到 notes.md 中的相关章节，或外部链接，或文件路径]
   - Last Updated: 2025-01-03

2. **[问题简述]**
   - Status: ⏳ Pending
   - Answer: (待回答)
   - Evidence / Reference: (待补充)
   - Last Updated: (待补充)
```

**状态说明：**
- ⏳ Pending: 尚未开始回答
- 🔄 In Progress: 正在研究中
- ✅ Answered: 已回答，答案已记录
- ❌ Dropped: 问题不再相关，已放弃

**实现位置：**
- 更新 `task_plan.md` 模板中的 "Key Questions" 部分
- 在 skill 的 "Core Workflow" 中添加规则：回答问题后必须更新此部分

---

## 优化后的完整模板

### task_plan.md 优化版模板

```markdown
# Task Plan: [Brief Description]

## Goal
[One sentence describing the end state]

## Phases
- [ ] Phase 1: Plan and setup
- [ ] Phase 2: Research/gather information
- [ ] Phase 3: Execute/build
- [ ] Phase 4: Review and deliver

## Key Questions & Answers

1. **[问题 1 简述]**
   - Status: ⏳ Pending
   - Answer: (待回答)
   - Evidence / Reference: (待补充)
   - Last Updated: (待补充)

2. **[问题 2 简述]**
   - Status: ⏳ Pending
   - Answer: (待回答)
   - Evidence / Reference: (待补充)
   - Last Updated: (待补充)

## Related Files

| File | Purpose | Last Updated | Status |
|------|---------|--------------|--------|
| notes.md | Research findings | (待补充) | Active |
| [deliverable].md | Final output | (待补充) | (待补充) |

**File Index Rules:**
- Add new files immediately after creation
- Update "Last Updated" when file is modified
- Status: Active / Reference / Complete / Archived

## Decisions Made
- [Decision]: [Rationale]

## Errors Encountered
- [Error]: [Resolution]

## Status
**Currently in Phase X** - [What I'm doing now]
```

---

## 修改建议

### 需要修改的文件

1. **`SKILL.md`** (主 skill 文件)
   - 更新 "task_plan.md Template" 部分
   - 在 "Critical Rules" 中添加更新检查点规则
   - 在 "Core Workflow" 中添加文件索引规则

2. **`reference.md`** (可选)
   - 可以添加关于文件索引和答案记录的说明

3. **`examples.md`** (可选)
   - 更新示例，展示新的模板使用方式

### 具体修改点

#### 修改点 1: SKILL.md - 更新模板

**位置：** `## task_plan.md Template` 部分

**原内容：**
```markdown
## Key Questions
1. [Question to answer]
2. [Question to answer]
```

**改为：**
```markdown
## Key Questions & Answers

1. **[问题简述]**
   - Status: ⏳ Pending / ✅ Answered / ❌ Dropped / 🔄 In Progress
   - Answer: (待回答或记录答案)
   - Evidence / Reference: (链接或引用)
   - Last Updated: (日期)

2. **[问题简述]**
   - Status: ⏳ Pending
   - Answer: (待回答)
   - Evidence / Reference: (待补充)
   - Last Updated: (待补充)
```

#### 修改点 2: SKILL.md - 添加 Related Files 部分

**位置：** `## task_plan.md Template` 部分，在 "Key Questions & Answers" 之后

**添加：**
```markdown
## Related Files

| File | Purpose | Last Updated | Status |
|------|---------|--------------|--------|
| notes.md | Research findings | (待补充) | Active |
| [deliverable].md | Final output | (待补充) | (待补充) |

**File Index Rules:**
- Add new files immediately after creation
- Update "Last Updated" when file is modified
- Status: Active / Reference / Complete / Archived
```

#### 修改点 3: SKILL.md - 增强 Critical Rules

**位置：** `## Critical Rules` 部分

**添加新规则：**
```markdown
### 6. Update Checkpoints
After EVERY major action, check if task_plan.md needs updating:
- [ ] Did I complete a phase? → Update checkbox
- [ ] Did I answer a key question? → Update Key Questions & Answers section
- [ ] Did I create a new file? → Add to Related Files section
- [ ] Did I encounter an error? → Add to Errors Encountered section
- [ ] Did I make a decision? → Add to Decisions Made section

**Update Frequency:**
- After each phase completion: MANDATORY
- After every 5-10 tool calls: RECOMMENDED
- Before any major decision: MANDATORY (read + update)
```

#### 修改点 4: SKILL.md - 更新 Core Workflow

**位置：** `## Core Workflow` 或 `### The Loop in Detail` 部分

**添加规则：**
```markdown
**When creating new markdown files:**
```bash
Write new_file.md
Edit task_plan.md  # Add to Related Files section
```

**When answering key questions:**
```bash
# After finding the answer:
Edit task_plan.md  # Update Key Questions & Answers section with answer
```
```

---

## 测试建议

### 测试场景 1：多文件创建
1. 创建一个复杂任务，生成多个 md 文件
2. 验证所有文件都被添加到 "Related Files" 部分
3. 新会话时，验证 AI 能够发现所有相关文件

### 测试场景 2：问题答案记录
1. 在 task_plan.md 中列出 3-5 个关键问题
2. 在任务执行过程中回答这些问题
3. 验证每个问题的答案都被记录在 task_plan.md 中
4. 新会话时，验证能够直接看到答案，无需查找历史

### 测试场景 3：更新检查机制
1. 执行一个包含 20+ 工具调用的任务
2. 验证 task_plan.md 在关键节点被更新
3. 验证不会出现"忘记更新"的情况

---

## 总结

### 优化效果预期

1. **问题 1（忘记更新）**
   - 通过明确的更新检查点，减少忘记更新的情况
   - 预期改善：70-80%

2. **问题 2（文件索引）**
   - 通过 Related Files 部分，所有文件都有索引
   - 预期改善：100%（完全解决）

3. **问题 3（答案记录）**
   - 通过 Key Questions & Answers 结构，答案直接记录
   - 预期改善：100%（完全解决）

### 注意事项

- 这些优化会增加一些维护成本（需要更新更多字段）
- 但带来的收益（信息可追溯性、新会话连续性）远大于成本
- 建议先在测试环境验证，确认效果后再应用到生产

---

## 附录：快速参考

### 状态图标说明
- ⏳ Pending: 尚未开始
- 🔄 In Progress: 进行中
- ✅ Answered: 已完成
- ❌ Dropped: 已放弃

### 文件状态说明
- Active: 正在使用中
- Reference: 参考文档
- Complete: 已完成
- Archived: 已归档

### 更新频率建议
- 阶段完成：必须更新
- 每 5-10 次工具调用：建议更新
- 重大决策前：必须读取并更新

---

**文档版本：** 1.0  
**创建日期：** 2025-01-XX  
**最后更新：** 2025-01-XX

