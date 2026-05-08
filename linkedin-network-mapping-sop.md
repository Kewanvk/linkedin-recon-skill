# 领英人物网络关系梳理 · 顶层 SOP

**输入**：一个岗位 / 一个 JD / 一个岗位 URL

**输出**：目标人物的立体画像 + 组织架构定位 + 人物关系网络 + 可执行的冷联系策略

**底层操作手册**：`relationship-mapping-sop.md`（五层方法论，每步的工具、代码模板、判断标准、踩坑清单都在里面）

---

## Step 1 · 从 JD 反查负责人

> 目标：找到最有可能发布这个岗位的人

### 做什么

从岗位信息出发，通过公开信息和领英数据，锁定这个岗位背后的真实负责人。

- 不是 HR recruiter（他们只是漏斗入口）
- 不是 job listing 上的 contact person（通常是 HR 或 TA team）
- 而是**真正有 hiring decision 的业务负责人**

### 怎么做

1. **从 JD 提取关键信号**
   - 岗位所属部门 / capability / practice 名称
   - 技术栈 / 项目关键词
   - 汇报对象描述（"reporting to..."）

2. **公开信息定位**
   - 公司官网 capability page（通常直接列出该方向的 contact partner，ROI 最高）
   - Google 搜索 "{Company} {capability} leader / head / partner"
   - 行业媒体 promotion 新闻

3. **LinkedIn 初步验证**
   - 用已知信息（名字 + 公司）在 LinkedIn 上确认身份
   - Apify `harvestapi/linkedin-profile-scraper` 拉完整 profile（$0.004/人）
   - 检查三项：现职位是否一致 / 是否还在目标公司 / tenure 多长

### 产出
- 1-3 个最可能的负责人名单
- 每人的基础信息（title / capability / LinkedIn URL / 验证状态）

### 关键判断
- 如果 JD 上有 "reporting to [Name]"，那个人大概率就是负责人
- 如果没有明确线索，优先看该 capability 的 service line page 上列出的 contact partner
- 找到的人需要 LinkedIn verify 才能进入 Step 2。没 verify 的信息只是 lead，不是 evidence

---

## Step 2 · 确认组织架构 + 横向纵向关联人

> 目标：把目标人放进组织坐标系里，确认他的上级、同级、下属中的关键关联人

### 为什么需要这一步

个体信息存在置信度偏差——一个人的 LinkedIn title 可能过期、部门可能调整、职责范围可能跟 title 不完全对应。**确认横向和纵向的关联人，是交叉验证的最有效手段。**

同时，关联人本身就是触达策略的扩展面——如果目标人不回复，他的同级或下属可能是备选路径。

### 怎么做

1. **向上确认**：目标人向谁汇报？
   - 看公司 leadership page 里的层级结构
   - LinkedIn profile 里的 title 暗示（Partner vs Director vs Manager）
   - 行业新闻里的 promotion 链条

2. **横向确认**：同 capability / 同层级还有谁？
   - 同一个 capability page 上列出的其他人
   - 同一个 promotion cohort（行业新闻里同批晋升的人）
   - LinkedIn 搜索同公司 + 相近 title

3. **向下探索**：目标人带的团队里有谁？
   - 这一步主要靠 Step 3 的帖子分析来发现
   - 也可以看 JD 里描述的团队规模和结构

4. **Apify batch verify**
   - 把 Step 1 的目标人 + Step 2 新发现的关联人一起 batch verify
   - $0.004/人，5 人 ≈ $0.02
   - 必须先 dry-run 1 条再 batch

### 产出
- 目标人在组织中的定位图（上级是谁、同级有谁、下属/团队成员有谁）
- 每个关联人的验证状态和置信度
- 初步的冷联系优先级判断（谁是主攻、谁是备选）

### 关键判断
- **Promotion 新闻 4 年后 ~40% 变化率**（Deloitte 案例实证）。2022 年晋升的人，到 2026 年可能已经升职、调任或离开。必须 verify。
- **已离开的人也是信号**。Heike Dekker-Schach 从 NL Consulting 离开去了 DU EMEA Paris——这意味着 Human Capital 方向可能有新负责人空缺。
- **矩阵组织的层级不是绝对的**。按 capability 主线分组，Industry 作为横切标签。

---

## Step 3 · 深度分析目标人

> 目标：从四个维度构建目标人的立体画像，挖掘他的人物关系网络

### 四个分析维度

#### (a) Posts 和评论

拉目标人的 LinkedIn 帖子（Apify `linkedin-profile-posts`，~$0.01/人），分析：

| 看什么 | 怎么用 |
|---|---|
| 发帖主题趋势 | 判断他现在关注什么领域，cold message 的钩子从这里来 |
| @提及的同事 | 发现 org chart 上看不到的真实协作关系 |
| 合作活动（co-lecture / panel / webinar） | 确认他的公开属性和学术/行业连接 |
| 帖子风格（formal vs casual） | 决定 cold message 的语气 |
| 发帖频率 | 高频 = 重视个人品牌 = 更容易通过内容互动建立关系 |

**案例**：Sjors Broersen 的 35 条帖子暴露了两个 org chart 上完全看不到的人——Riona Arjoon（"expert colleague"，AI governance in banking）和 Gideon Franken（Erasmus 客座讲课的 co-presenter）。

#### (b) 个人简历信息

从 LinkedIn profile 的完整 timeline 提取：

| 看什么 | 怎么用 |
|---|---|
| 教育背景 | 找共同点（同校 / 同专业 / 同地区）作为 cold message 的共鸣点 |
| 职业路径 | 判断他是内部晋升还是外部空降——内部晋升的人更了解组织文化 |
| 非传统背景 | 天文学 PhD 转 AI（Sjors）、创业者做咨询（Hermes）——这些人对非传统背景的候选人更 open |
| tenure | 待了多久决定稳定度和组织影响力 |
| 技能标签 | 跟 JD 要求做匹配度分析 |

#### (c) 网络上的公开发言

超越 LinkedIn，到更广的公开信息里找：

| 数据源 | 怎么搜 |
|---|---|
| 行业会议 speaker list | Google "{Name} speaker / panelist / keynote" |
| 播客 / 视频 | Google "{Name} podcast / interview / webinar" |
| 媒体引用 | Google "{Name} {Company} quote / interview / article" |
| 学术 / 教学 | Google "{Name} lecture / course / university" |
| GitHub / 技术社区 | 如果是技术岗，看他的开源贡献 |

**用法**：公开发言里的具体观点是 cold message 最强的钩子。"我看了您在 X 会议上关于 Y 的演讲"比"我对您的工作很感兴趣"强 10 倍。

#### (d) 评论区人物关系网络

从目标人的帖子评论区、合作活动的参与者列表中，挖掘更广的人物网络：

| 信号 | 含义 | 怎么用 |
|---|---|---|
| 频繁互评的人 | 真实工作关系（不只是社交礼貌） | 可能是团队成员或密切合作者 |
| 上级点赞 / 评论 | 上级认可这个方向 | 验证目标人在组织内的影响力 |
| 外部专家互动 | 行业圈层连接 | 判断目标人的外部网络密度 |
| 求职者互动 | 目标人是否回复求职相关评论 | 判断他对 cold reach 的开放度 |

### 产出

每个目标人一份完整画像，包含：

1. **基础信息**：title / capability / LinkedIn URL / followers / 验证状态
2. **职业路径摘要**：教育 → 关键转折 → 当前角色
3. **内容偏好**：帖子主题趋势 + 公开发言锚点
4. **人物关系网络**：通过帖子/评论发现的关联人
5. **冷联系策略**：Path A/B/C + 难度判断（R1 follower 规则）+ cold message 草稿
6. **cold message 草稿**：100-150 词，含 R7 三件套（引用具体内容 + 30 分钟 ask + 互利）

---

## 三步之间的信息流

```
JD URL
  │
  ▼
Step 1 · 反查负责人
  │  产出：1-3 个候选负责人
  │
  ▼
Step 2 · 确认组织架构
  │  产出：目标人定位图 + 横向纵向关联人
  │  （这一步的关联人发现会反哺 Step 1 的判断——
  │   如果发现负责人判断有误，回到 Step 1 修正）
  │
  ▼
Step 3 · 深度分析
  │  产出：立体画像 + 人物关系网络 + 冷联系策略
  │  （这一步通过帖子发现的新人物会反哺 Step 2 的组织图——
  │   Sjors 的帖子发现 Riona 和 Gideon，补全了 AI & Data 团队图谱）
  │
  ▼
最终交付：可视化关系图谱 + 冷联系行动 plan
```

**反馈环路**：三步不是纯线性的。Step 2 和 Step 3 的发现可能修正 Step 1 的判断。但总流向是 1→2→3，回溯只在发现明显偏差时触发。

---

## 工具成本总览

| 步骤 | 工具 | 成本 |
|---|---|---|
| Step 1 | WebSearch + WebFetch + Apify profile scraper (1-3 人) | $0.004-0.012 |
| Step 2 | WebSearch + Apify batch verify (3-5 人) | $0.012-0.020 |
| Step 3 | Apify posts scraper (1-2 人) + WebSearch | $0.010-0.020 |
| **合计** | | **$0.03-0.05** |

全流程 1.5-3 小时，总成本 < $0.05。

---

## 质量检查清单

交付前过一遍：

- [ ] 所有目标人都经过 LinkedIn verify（不能只有二手数据）
- [ ] 每个 cold message 都包含 R7 三件套（具体引用 + 30 分钟 ask + 互利）
- [ ] 已离开的人明确标注（不能把已离职的人当在职来推荐）
- [ ] 置信度标注完整（高/中/低）
- [ ] 推断和事实分开标注（推断不进 cold message）
- [ ] 关系图谱节点和边的类型标注正确

---

## 文件关系

```
recruiting-recon-v1.0/
├── proposal.md                      ← 7 步工序 + 产出规范 + 判断规则 + anti-pattern
├── linkedin-network-mapping-sop.md  ← 本文件（顶层三步流程）
├── relationship-mapping-sop.md      ← 底层五层方法论（每步的操作手册）
└── product-doc.md                   ← 面向用户的产品文档
```

---

## 版本记录

| 版本 | 日期 | 变动 |
|---|---|---|
| v1.0 | 2026-05-08 | 从 4 个案例提炼，三步顶层流程 + 引用五层底层操作手册 |
