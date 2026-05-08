# 流程约束 · 渐进式披露与文档留存

**硬规则**：每一个环节的中间产物必须落盘为独立 md 文件。不允许跳步、不允许只出最终交付物。

**原则**：渐进式披露（Progressive Disclosure）——每一步只处理该步骤范围内的信息，产出该步骤的文档后，才进入下一步。上一步的文档是下一步的输入依据。

---

## 项目目录结构（每个 case 必须遵守）

```
recruiting-recon-{company}-{date}/
│
├── 00-input.md                  ← Step 0：输入校准
├── 01-jd-signal.md              ← Step 1a：JD 信号提取
├── 01-candidate-leads.md        ← Step 1b：候选负责人名单
├── 01-verify.json               ← Step 1c：Apify 验证原始数据
│
├── 02-org-map.md                ← Step 2a：组织架构映射
├── 02-related-persons.md        ← Step 2b：横向纵向关联人
├── 02-verify-batch.json         ← Step 2c：Apify batch 验证原始数据
├── 02-confidence-matrix.md      ← Step 2d：置信度矩阵
│
├── 03-posts-analysis.md         ← Step 3a：帖子与评论分析
├── 03-profile-deep.md           ← Step 3b：简历深度分析
├── 03-public-presence.md        ← Step 3c：公开发言汇总
├── 03-network-map.md            ← Step 3d：评论区人物关系网络
├── 03-portraits.md              ← Step 3e：目标人立体画像（汇总）
│
├── deliverable/
│   ├── index.html               ← 最终交付：战略地图 HTML
│   └── relationship-graph.html  ← 最终交付：关系图谱可视化
│
├── raw/                         ← Apify / 爬虫原始数据（json）
│   ├── profiles-batch.json
│   ├── posts-{name}.json
│   └── ...
│
└── diary.md                     ← 工序日志（bug / 决策变更 / 学到的）
```

---

## 每个文件的产出规范

### 00-input.md · 输入校准

在任何操作之前必须先写这个文件。内容：

```markdown
# Input

- **岗位/JD**：[URL 或描述]
- **目标公司**：[名称 + 国家/地区]
- **Persona**：[个人定制 / generic playbook]
- **Entry layer**：[intern / ECP / grad rotation / lateral / fellowship]
- **Anchor**：[有/没有，如有列出名字和关系]
- **范围约束**：[哪些方向不看]
- **预算约束**：[Apify 预算上限，默认 $0.10]
```

**约束**：用户确认此文件后才能进入 Step 1。

---

### 01-jd-signal.md · JD 信号提取

```markdown
# JD Signal Extraction

## 岗位基础信息
- Title:
- Department / Capability:
- Location:
- 汇报对象（如 JD 中提及）:

## 关键信号
| 信号类型 | 原文摘录 | 推断 |
|---|---|---|
| 技术栈 | "..." | → 可能的团队方向 |
| 项目关键词 | "..." | → 所属 capability |
| 经验要求 | "..." | → entry layer 判断 |
| 团队描述 | "..." | → 团队规模 / 负责人线索 |

## 初步推断的负责人线索
- 线索 1：[来源 + 推断 + 置信度]
- 线索 2：...
```

---

### 01-candidate-leads.md · 候选负责人名单

```markdown
# Candidate Leads

| # | Name | Title | Source | LinkedIn URL | 置信度 | 下一步 |
|---|---|---|---|---|---|---|
| 1 | ... | ... | capability page | ... | 高/中/低 | verify |
| 2 | ... | ... | Google search | ... | ... | verify |

## 判断依据
- 为什么认为 #1 最可能是负责人：[具体理由]
```

---

### 01-verify.json · Apify 验证原始数据

Apify `linkedin-profile-scraper` 的 raw JSON 输出，不做任何加工。留存备查。

---

### 02-org-map.md · 组织架构映射

```markdown
# Org Map · {Company}

## 架构
Executive Board
├── [Name] · [Title]
└── ...

{Capability A}
├── [Name] · [Title] · [验证状态]
└── ...

## Persona Fit 矩阵
| Capability | Fit | 理由 |
|---|---|---|
| ... | 高/中/低/不匹配 | ... |

## Sweet Spots（收窄后的 3-5 个方向）
1. ...
2. ...
```

---

### 02-related-persons.md · 横向纵向关联人

```markdown
# Related Persons · {Target Name}

## 向上（汇报对象）
- [Name] · [Title] · [来源] · [置信度]

## 横向（同级）
- [Name] · [Title] · [来源] · [置信度]

## 向下（团队成员）
- [Name] · [Title] · [来源] · [置信度]
- （如无公开数据，标注"待 Step 3 帖子分析发现"）

## 发现的信息修正
- [谁的信息跟 Step 1 不一致，具体差异]
```

---

### 02-confidence-matrix.md · 置信度矩阵

```markdown
# Confidence Matrix

| Name | 公司官网 | 行业新闻 | LinkedIn Verify | 综合置信度 | 能进邮件吗 |
|---|---|---|---|---|---|
| ... | Y/N | Y/N | Y/N | 高/中/低 | Y/N |

## 已离开的人
| Name | 原职位 | 现去处 | 发现来源 |
|---|---|---|---|

## 找不到的人
| Name | 尝试了什么 | 结论 |
|---|---|---|
```

---

### 03-posts-analysis.md · 帖子与评论分析

```markdown
# Posts Analysis · {Name}

**数据来源**：Apify linkedin-profile-posts · [N] 条帖子 · 时间范围 [earliest] — [latest]

## 主题趋势
| 主题 | 出现次数 | 代表帖子摘要 |
|---|---|---|

## 同事提及
| 被提及的人 | 原文 | 关系类型 | 后续动作 |
|---|---|---|---|
| ... | "expert colleague ..." | 深度合作 | 加入 network map |

## 合作活动
| 活动 | 合作者 | 日期 | 类型 |
|---|---|---|---|

## Cold message 可用的钩子
- 钩子 1：[引用哪条帖子的什么内容]
- 钩子 2：...
```

---

### 03-profile-deep.md · 简历深度分析

```markdown
# Profile Deep Dive · {Name}

## Timeline
| 时间 | 角色 | 组织 | 备注 |
|---|---|---|---|

## 教育背景
- ...

## 关键转折点
- [哪一步转折最能跟 persona 产生共鸣]

## 非传统信号
- [如有：跨领域背景 / 创业经历 / 学术转商业]

## 跟 Persona 的共鸣点
- ...
```

---

### 03-public-presence.md · 公开发言汇总

```markdown
# Public Presence · {Name}

| 类型 | 来源 | 日期 | 主题 | URL | 可用于 cold message |
|---|---|---|---|---|---|
| 会议演讲 | ... | ... | ... | ... | Y/N |
| 播客 | ... | ... | ... | ... | Y/N |
| 媒体引用 | ... | ... | ... | ... | Y/N |

## 没找到的
- [搜了但没找到公开发言 → 这本身是信号：对方不公开活跃]
```

---

### 03-network-map.md · 评论区人物关系网络

```markdown
# Network Map · {Name}

## 通过帖子/评论发现的关联人
| 关联人 | 关系类型 | 证据 | 置信度 | 是否加入分析 |
|---|---|---|---|---|

## 关系类型说明
- 深度合作：co-author / expert colleague / 共同项目
- 浅层连接：co-lecturer / 同 panel
- 社交互动：频繁互评但无实质合作证据

## 网络密度判断
- [目标人的网络是封闭的（团队内循环）还是开放的（跨组织连接多）]
- [这对冷联系策略的影响]
```

---

### 03-portraits.md · 目标人立体画像（汇总）

```markdown
# Portraits

## {Name 1} · Priority #{N}

### 基础信息
- Title / Capability / LinkedIn / Followers / 验证状态

### 为什么是 ta
- [具体理由，不能笼统]

### 画像摘要
- 职业路径：...
- 内容偏好：...
- 人物网络：...

### 冷联系策略
- Path：A / B / C
- 难度：低 / 中 / 高（R1 follower 规则）
- 时机：...
- Fallback：...

### Cold Message 草稿
> [100-150 词，含 R7 三件套]

---

## {Name 2} · Priority #{N}
...
```

---

### diary.md · 工序日志

```markdown
# Diary · {Company} · {Date}

## 动了什么
| 时间 | 动作 | 结果 |
|---|---|---|

## 没动什么
- [明确记录没做的事和原因]

## 未落决策
- [需要用户确认的事项]

## 边缘情况
- [踩到的坑 / 意外发现 / 工具 bug]

## 成本记录
| 工具 | 调用次数 | 成本 |
|---|---|---|
```

---

## 流程约束（硬规则）

1. **每一步完成后，对应的 md 文件必须写入磁盘，才能进入下一步**。不允许"先跑完再补文档"。

2. **文件是渐进式生长的**。Step 1 的文件在 Step 2 发现新信息时可以回去更新，但更新要在原文件末尾加 `## 更新记录` 标注修改了什么、为什么。

3. **raw 数据和分析文档分离**。Apify 返回的 JSON 原样存 `raw/`，分析结论写到对应的 md 里。不在 JSON 里直接标注。

4. **diary.md 必须同时记录四件事**：动了什么 / 没动什么 / 未落决策 / 边缘情况。缺任何一项都不完整。

5. **deliverable/ 里的最终交付物只能引用前面 md 里已经确认的信息**。不允许在最终交付物里出现前面步骤文档中没有的新信息。

6. **每个 Apify 调用前先记录预期成本，调用后记录实际成本**。写在 diary.md 的成本记录表里。

---

## 版本记录

| 版本 | 日期 | 变动 |
|---|---|---|
| v1.0 | 2026-05-08 | 初版，基于三步顶层 SOP + 四个案例经验 |
