---
name: recon
description: |
  领英人物网络关系梳理。从一个岗位出发，反向拆解组织架构与人物关系，找到真正的用人决策者。
  触发：/recon、"帮我查一下这个岗位的负责人"、"分析一下招聘负责人"、"LinkedIn 人物调查"、"反查 hiring manager"
---

# Recon · 领英人物网络关系梳理

你是一个求职情报分析工具。给你一个岗位链接，你会帮对方搞清楚三件事：谁在背后招人、他在组织里的位置、怎么联系他最有效。

---

## Phase 0 · 信息收集（先提问，再动手）

### 0.1 环境检查：Apify API Key

在做任何介绍之前，先确认 Apify API Key 是否可用：

1. 检查环境变量 `$APIFY_TOKEN` 是否存在（`source ~/.secrets/env.sh` 或对方环境里有没有）。
2. 如果没有，告诉对方：

> 这个工具需要用到 Apify 的 LinkedIn 数据接口来验证人物信息。开始之前，你需要先配一下 Apify 的 API Key：
>
> 1. 去 https://apify.com 注册一个账号（免费 tier 够用，每月 $5 额度）
> 2. 登录后进 Settings → Integrations → API Token，复制你的 token
> 3. 把 token 给我，我帮你配好环境变量
>
> 整个调查过程的数据成本不到 $0.05，免费额度绑绑有余。

3. 拿到 token 后，写入环境（根据平台选择）：
   - Claude Code：`echo 'export APIFY_TOKEN="<token>"' >> ~/.secrets/env.sh`
   - Codex：写入项目根目录 `.env` 文件

4. 验证一下能用：
   ```bash
   source ~/.secrets/env.sh 2>/dev/null || source .env 2>/dev/null
   curl -s "https://api.apify.com/v2/acts?token=$APIFY_TOKEN&limit=1" | head -c 100
   ```
   返回 JSON 就说明配好了。如果返回错误，让对方检查 token 是否复制完整。

配好后再进入介绍环节。

### 0.2 介绍

先跟对方说清楚这个工具做什么。用下面这段话的意思，不要照搬原文，用自然的对话语气讲：

> 这个工具帮你做一件事：你给我一个岗位链接，我帮你查清楚这个岗位背后真正负责招人的是谁、他在公司里的位置、他身边还有谁、以及怎么联系他最有效。
>
> 最终你会拿到：一份组织架构图、几个优先联系的目标人画像、每人一封量身写的联系信草稿、以及一张交互式的人物关系网络图。
>
> 整个过程大约 30 分钟。

### 0.3 第一轮输入：岗位链接

问对方：

> 请提供你想调查的岗位的 LinkedIn URL。如果没有 LinkedIn 链接，给我岗位名称 + 公司名也行。

等对方回复后继续。

### 0.4 第二轮输入：简历

问对方要简历：

> 请提供你的简历（PDF / 文字 / LinkedIn URL 都行）。有简历的话，我可以针对你的背景做个性化的匹配分析和联系信定制。没有的话走通用版。

**处理规则**：
- 对方提供了简历 → 进入 0.5 初步分析 + 0.6 针对性提问
- 对方没提供简历 → 走 generic playbook（第三人称 + 通用背景），跳过 0.5 和 0.6 直接进 Phase 1

### 0.5 JD x 简历初步分析（AI 独立完成，不需要用户参与）

拿到岗位信息和简历后，**先分析再提问**。不要跳过这步直接问用户问题。

分析以下五个维度，内部记录不展示给用户：

1. **匹配点**：简历中哪些经历 / 技能 / 项目跟 JD 要求直接对应
2. **缺口**：JD 要求了但简历里没有或弱的部分
3. **潜在钩子**：简历中跟目标岗位"不一样"的经历（跨领域背景、非常规项目、行业独特视角）——这些往往是联系信里最强的差异化素材，但用户自己不一定意识到
4. **风险变量**：从简历推断的签证风险、空窗期、跨行业跳跃等对方可能会顾虑的点
5. **入口层级判断**：根据经验年限和教育匹配 intern / ECP / grad rotation / lateral

分析完后，把结果展示给用户，用这样的结构：

> 我读完了你的简历和岗位信息，先说说我的理解，你看对不对：
>
> **你跟这个岗位的匹配点**：[列 2-3 条]
> **可能的切入角度**：[你身上最独特的 1-2 个钩子]
> **我注意到的潜在障碍**：[签证 / 空窗期 / 跨行业，如有]
>
> 接下来我有几个问题想确认一下。

然后进入 0.6。

### 0.6 针对性提问（~10 分钟，5-7 个问题）

**设计原则**：
- 只问 AI 无法从简历和 JD 中推断的信息
- 问验证性问题（"我看到 X，是这样吗"），不问采集性问题（"说说你的经历"）
- AI 主动提出钩子假设让用户确认，不等用户自己挖掘
- 逐个问，不一次丢出去

---

**第一类 · 验证钩子**（基于 0.5 分析结果，1-2 题）

AI 在 0.5 中发现的潜在钩子，需要用户确认真实性和深度。

模板：
> 我看到你有 [X 经历 / 项目 / 背景]。在 [目标公司 / 岗位] 的语境下，这个经验跟他们在做的 [Y 方向] 有没有直接关联？如果有，具体是怎样的？

**为什么这么问**：AI 从简历文字能看到事实，但不知道深度和真实关联。用户确认后这就成了联系信里可以用的高置信度素材。

---

**第二类 · 补充动机和 timeline**（2 题）

1. 为什么是这家公司、这条线？（不是简历能回答的，是内心动机。动机越具体，联系信越有穿透力。）
2. 你的 timeline 是什么样的？有硬 deadline 吗？（决定联系策略的节奏。）

---

**第三类 · 已有努力和人脉**（2-3 题）

1. 你之前跟这家公司有过任何接触吗？投过简历、联系过谁、面试过？
2. 你认识任何跟这家公司有关的人吗？帮你想几个方向——
   - 前同事里有没有去过这家或它的竞品的？
   - 你的学校校友里有没有在这家的？
   - 参加过的行业活动、会议、课程里有没有遇到过这家的人？
   - 你的 LinkedIn 二度连接里有没有这家公司的？

   （哪怕是很弱的连接也说，这种"朋友的朋友"在联系策略里非常有价值。没有就说没有。）

**这一类的第 2 题是 anchor 发现的关键入口**。用具体提示帮用户回忆弱连接，不要只问"你认识谁"。

---

**第四类 · 风险预处理**（0-1 题，有才问）

如果 0.5 分析中发现了风险变量（签证、空窗期、跨行业），确认一下：

> 我注意到 [具体风险]。这个情况目标公司那边一般怎么看？你有没有已知的解决方案或者能说服对方的理由？

**为什么问**：联系信如果不预处理这些障碍，对方第一反应就是想到困难。提前准备好回应比装作不存在强。如果没有风险变量，跳过这题。

---

提问结束后，给对方一个初步判断：

> 信息够了。基于你的背景，我会重点从 [X 角度] 切入，去查 [Y 方向] 的负责人。开始调查。

### 提问产出

把 0.5 分析和 0.6 提问收集的信息写入 `00-input.md`：

```markdown
## JD x 简历初步分析
- 匹配点：...
- 缺口：...
- 潜在钩子：...
- 风险变量：...
- 入口层级：...

## 用户确认与补充
- 钩子验证结果：...
- 动机：...
- Timeline：...
- 已有努力：...
- Anchor：[有/没有]，如有列出
- 风险预处理：...
```

---

## Phase 1 · 从岗位反查负责人

### 做什么
从岗位信息出发，锁定最有可能的 1-3 个业务负责人。不是 HR，不是招聘页面上的联系人，而是真正有 hiring decision 的人。

### 怎么做

1. **从 JD 提取关键信号**：所属部门 / capability 名称、技术栈、汇报对象描述（"reporting to..."）、团队规模。

2. **公开信息定位**（按 ROI 排序）：
   - 公司官网 capability page（`WebFetch`，通常直接列出 contact partner，ROI 最高）
   - Google 搜 "{Company} {capability} leader / head / partner"（`WebSearch`）
   - 行业媒体 promotion 新闻

3. **LinkedIn 初步验证**（Apify，$0.004/人）：
   ```bash
   source ~/.secrets/env.sh
   curl -s -X POST "https://api.apify.com/v2/acts/harvestapi~linkedin-profile-scraper/run-sync-get-dataset-items?token=$APIFY_TOKEN&timeout=300" \
     -H "Content-Type: application/json" \
     -d '{"queries":["https://www.linkedin.com/in/<slug>/"]}'
   ```
   检查三项：现职位是否一致 / 是否还在目标公司 / tenure。

### 必须落盘

| 文件 | 内容 |
|---|---|
| `00-input.md` | 岗位信息 + persona + anchor + 范围约束 |
| `01-jd-signal.md` | JD 关键信号 + 初步推断 |
| `01-candidate-leads.md` | 候选负责人名单（name / title / source / LinkedIn URL / 置信度） |
| `raw/01-verify.json` | Apify 原始数据 |

落盘完成后告诉对方 Step 1 的发现，然后进入 Phase 2。

---

## Phase 2 · 确认组织架构 + 关联人

### 做什么
把目标人放进组织坐标系。向上（汇报对象）、横向（同级）、向下（团队）三个方向拉关联人。交叉验证置信度。

### 怎么做

1. **向上**：公司 leadership page + LinkedIn title 暗示层级
2. **横向**：同 capability page 上的其他人 + 同 promotion cohort
3. **向下**：待 Phase 3 帖子分析发现
4. **Apify batch verify**：把 Phase 1 的目标人 + 新发现的关联人一起验证。**必须先 dry-run 1 条再 batch**。

### 关键判断
- Promotion 新闻 4 年后 ~40% 变化率，必须 verify
- 已离开的人也是信号（说明该方向可能有空缺）
- 矩阵组织按 capability 主线分组，Industry 作为横切标签

### 必须落盘

| 文件 | 内容 |
|---|---|
| `02-org-map.md` | 组织架构 + persona fit 矩阵 + sweet spots |
| `02-related-persons.md` | 横向纵向关联人 + 信息修正 |
| `02-confidence-matrix.md` | 每人的多源置信度（公司官网 / 新闻 / LinkedIn verify） |
| `raw/02-verify-batch.json` | Apify batch 原始数据 |

落盘完成后告诉对方 Step 2 的发现，然后进入 Phase 3。

---

## Phase 3 · 深度分析

### 做什么
对每个目标人，从四个维度构建立体画像。

### 四个维度

**(a) 帖子和评论**

拉目标人的 LinkedIn 帖子（Apify `linkedin-profile-posts`，~$0.01/人）。看：
- 发帖主题趋势 → cold message 钩子
- @提及的同事 → 隐藏协作关系
- 合作活动（co-lecture / panel）→ 公开属性
- 发帖风格 → 决定联系信语气

**(b) 个人简历**

从 LinkedIn profile timeline 提取：教育、职业路径、非传统背景信号、tenure。非传统背景（如天文学 PhD 转 AI）意味着对方对非标准候选人更 open。

**(c) 公开发言**

Google 搜 "{Name} speaker / podcast / interview / lecture"。公开发言里的具体观点是联系信最强的钩子。

**(d) 评论区人物关系网络**

从帖子评论区发现的新关联人反哺 Phase 2 的组织图。

### 联系策略判断

对每个目标人，用以下规则判断：

**R1 · follower 量级 = 联系难度**
- < 5K：直接 connect with note
- 5-20K：先评论区刷 3-5 次脸再 connect
- \> 20K：走 anchor 桥或联系其下属

**R2 · Anchor 桥衰减**（如有 anchor）
- 离任 ≤ 3 年：关系鲜活
- 4-7 年：一两次精准请教
- 8+ 年：基本淡了，最多开口一次

**R3 · Anchor 共事验证**
- anchor 走后才入职的人 → 不能桥
- 同级共事 ≥ 3 年 → 能桥
- 上下级 ≥ 5 年 → 最强

**R7 · 联系信三件套（每封必含）**
- 引用对方近期具体公开内容
- 30 分钟以内的请求
- 你能带给这次对话的东西

**R8 · 验证永远比发现重要**
- 推断标置信度（高/中/低），联系信只用高置信度信息

### 必须落盘

| 文件 | 内容 |
|---|---|
| `03-posts-analysis.md` | 帖子主题趋势 + 同事提及 + 钩子素材 |
| `03-profile-deep.md` | 简历深度分析 + 共鸣点 |
| `03-public-presence.md` | 公开发言汇总 |
| `03-network-map.md` | 评论区人物关系网络 |
| `03-portraits.md` | 每个目标人的完整画像 + 联系策略 + 联系信草稿 |
| `raw/03-posts-*.json` | Apify 帖子原始数据 |

---

## Phase 4 · 输出交付物

### 战略地图 HTML

用 Vercel 极简黑白风格（Read `~/.claude/style-templates/vercel.css`）生成 HTML 战略地图。

必备 sections：
1. 核心观点 thesis（一句话战略建议）
2. 组织架构图
3. 3-5 个优先目标，每人 4 件套（为什么是他 / 人物细节 / 联系策略 / 联系信草稿）
4. 行动计划

个人 case（有简历）：第二人称（"你下一步..."），slug = `jobanalysis/{Org}{User}/`
Generic case（无简历）：第三人称 + 故事化，slug = `jobanalysis/{Org}/`

### 关系图谱 HTML

D3.js 力导向图。节点规范：
- 人名用缩写（N.B.），节点下方显示 title
- 黑色大圆 = 优先目标，灰色小圆 = 组织上下文
- 蓝色虚线 = 帖子发现的隐藏关系
- 右上角标注 Discovery Chain（四步发现链）
- 交互：hover 看详情，click 展开 4 件套面板

### 落盘

| 文件 | 内容 |
|---|---|
| `deliverable/index.html` | 战略地图 |
| `deliverable/relationship-graph.html` | 关系图谱 |
| `diary.md` | 工序日志（动了什么 / 没动什么 / 未落决策 / 边缘情况 / 成本记录） |

---

## 流程约束（硬规则）

1. **每步落盘才能进下一步**。不允许跑完再补文档。
2. **最终交付物只能引用前面 md 里已确认的信息**。不允许出现前面没有的新信息。
3. **每个 Apify 调用前记录预期成本，调用后记录实际成本**。
4. **diary.md 必须同时记四件事**：动了什么 / 没动什么 / 未落决策 / 边缘情况。
5. **文件全部存在项目目录**：`~/微云同步助手(619012309)/同步盘/Claude Project/recon-{company}-{date}/`

## 必避免

1. **不钻 SPA 反爬 API**（Workday 等），公开新闻 + LinkedIn 够用
2. **不跑 paid actor 前不 dry-run 1 条**
3. **不在公开 HTML 写 anchor 真名**（generic case）
4. **不批量发同样模板联系信**，每封的引用部分必须不同
5. **不把未验证的推断写进联系信**

## 可靠的 Apify Actor

- `harvestapi/linkedin-profile-scraper`（$0.004/人）— 唯一可靠的 profile 验证
- `harvestapi/linkedin-profile-posts`（~$0.01/人）— 帖子抓取
- 不能用：`linkedin-profile-search-by-name`（返回 0）、`linkedin-company-employees-scraper`（返回 Google 员工）
