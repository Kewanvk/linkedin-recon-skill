# 领英人物网络关系梳理 SOP

**定位**：跟 `proposal.md` 的 7 步工序并列。proposal 讲"做什么"，这份讲"关系那一步具体怎么做"。

**来源**：从 4 个跑通案例（Lee Howell BD · Deloitte NL 求职 · WEF 组合 · Villars BD）中提炼。

---

## 总览：五层方法论

关系梳理不是一个动作，是五层叠加的过程。每层解决一个不同的问题：

| 层 | 解决什么问题 | 输入 | 输出 |
|---|---|---|---|
| 1. 层级映射 | 组织里谁管谁 | 公开网页 | Org chart + 名单 |
| 2. 帖子网络扩展 | 名单上看不到的真实协作圈 | LinkedIn posts | 隐藏关系 + 新人物 |
| 3. 时间重叠分析 | Anchor 能桥到谁 | LinkedIn timeline | 桥/不能桥的判定 |
| 4. 多源交叉验证 | 哪些信息能信 | 多来源比对 | 置信度标注 |
| 5. 可达性评估 | 怎么联系到每个人 | 前四层汇总 | Path A/B/C + 优先级 |

**执行顺序**：严格按 1→2→3→4→5，不能跳。每层的输出是下一层的输入。

---

## Layer 1 · 层级映射（Hierarchical Mapping）

> 回答的问题：**这个组织里谁管谁、谁在哪个格子里？**

### 输入
- 组织名 + 国家/地区
- 用户 persona（用来做 fit 过滤）

### 工具 & 数据源（按 ROI 排序）

| 数据源 | 怎么用 | ROI | 置信度 |
|---|---|---|---|
| **Service line capability page** | `WebFetch` 公司官网各 capability 的单独页面。这种页面通常直接列出该方向的 contact partner（5-10 人 + 邮箱） | ★★★★★ | 高 |
| Leadership / About page | `WebFetch` 公司官网的 leadership 或 about 页 | ★★★★ | 高 |
| 行业媒体 | `WebSearch` consultancy.eu / consultancy.{country} 的 promotion 新闻 | ★★★ | 中（4 年后 ~40% 变化率） |
| Google 组合搜索 | `WebSearch` "{Company} {Region} leadership team 2026" | ★★★ | 中 |
| Careers portal | `WebFetch` 公司招聘页，看在招岗位推断组织结构 | ★★ | 中 |

### 操作步骤

1. **先抓 capability page**，不是 leadership page。这是从 Deloitte 案例学到的最重要一条——capability page 的 contact partner 比 generic leadership 页精准得多、覆盖中层。

2. **再抓 leadership page** 补全高层（CEO/COO/Board）。

3. **行业新闻补全中层**。搜近 2 年的 promotion / new hire 新闻。注意：这些信息只是 leads，不是 evidence。

4. **按 capability 分组建 org chart**：
   ```
   Executive Board
   ├── Capability A（列出 Partner + Lead）
   ├── Capability B
   └── ...
   Industry Verticals（横切）
   ├── Industry X（标注跨 capability 的人）
   └── ...
   ```

5. **用 persona 做 fit 过滤**，标注每个 capability 跟用户的匹配度（高/中/低/不匹配）。收窄到 3-5 个 sweet spot。

### 产出
- `org-map.md`：分组名单 + 每人基础信息 + fit 标注
- 收窄后的 sweet spot 列表

### 踩坑清单
- 矩阵组织（如 Deloitte 的 Capability × Industry）单线汇报无法 100% 锁定。按 capability 主线分组，Industry 作为横切标签，承认不绝对。
- 公开 leadership page 可能滞后 3-12 个月。不要把页面上的信息当最终真相，Layer 4 会验证。
- **不要钻 SPA 反爬 API**（Workday CXS 等）。WEF 案例烧了 $2.4 在 Workday API 上，拿到 0 有效数据。公开新闻 + LinkedIn 够用。

---

## Layer 2 · 帖子网络扩展（Post-based Network Expansion）

> 回答的问题：**组织图上看不到的真实协作关系是什么？**

### 为什么需要这一层
Layer 1 给的是"官方组织图"。但真实的协作关系（谁跟谁一起做项目、谁提到谁是"expert colleague"、谁跟谁一起讲课）只有通过 LinkedIn 帖子才能看到。

Deloitte 案例的关键发现：Sjors Broersen 的 35 条帖子暴露了两个 org chart 上完全看不到的人——Riona Arjoon（AI governance in banking，被 Sjors 称为"expert colleague"）和 Gideon Franken（Erasmus 客座讲课的 co-presenter）。这两个人后来成了冷联系策略的重要变量。

### 输入
- Layer 1 产出的 sweet spot 内的关键人物名单（3-5 人）

### 工具

```bash
# 拉目标人的 LinkedIn 帖子
source ~/.secrets/env.sh
curl -s -X POST "https://api.apify.com/v2/acts/harvestapi~linkedin-profile-posts/run-sync-get-dataset-items?token=$APIFY_TOKEN&timeout=300" \
  -H "Content-Type: application/json" \
  -d '{
    "profileUrls": ["https://www.linkedin.com/in/<slug>/"],
    "maxPosts": 50
  }' > posts-{name}.json
```

成本：约 $0.01-0.02 / 人。

### 操作步骤

1. **选 1-2 个 sweet spot 内最关键的人**拉帖子（不是全拉——成本控制）。优先选：公开发言多的、title 里有 "Public Speaker" 的、follower > 1000 的。

2. **扫描帖子内容，标记三种信号**：

   | 信号类型 | 怎么认 | 例子（Deloitte case） |
   |---|---|---|
   | **同事提及** | "@某人" 或 "my colleague X" 或 "expert colleague" | Sjors → "expert colleague Riona" |
   | **合作活动** | "co-presented with" / "guest lecture with" / "panel with" | Sjors × Gideon @ Erasmus 客座讲课 |
   | **兴趣/立场信号** | 帖子主题趋势（AI governance、agentic AI、某行业） | Sjors 持续发 agentic AI 内容 |

3. **对发现的新人物，走 Layer 1 的基本流程**：Google 搜名字 + 公司确认身份 → 加入 org map。

4. **记录关系类型**。不是所有关系都一样：
   - "expert colleague" = 深度合作，可能共同做项目
   - "guest lecture co-presenter" = 浅层，但说明在同一领域
   - "@提及" = 可能只是社交礼貌

### 产出
- 新发现的人物列表 + 跟已知人物的关系类型
- 关键人物的帖子主题趋势（用于写 cold message 的钩子）

### 踩坑清单
- 帖子里的人名可能不完整（只有 first name）。需要 cross-reference 公司 + 领域确认全名。
- 评论区互动不等于同事关系。分清"社交点赞"和"实质合作"。

---

## Layer 3 · 时间重叠分析（Temporal Overlap Analysis）

> 回答的问题：**如果有 anchor（ex-org 的关系人），ta 能桥到谁、不能桥到谁？**

### 前提
这一层只在有 anchor 的 case 里执行。没有 anchor → 跳过这层，所有目标走 Path A（直接 cold reach）。

### 输入
- Anchor 的 LinkedIn timeline（在目标组织的起止年份）
- 目标人物列表的 LinkedIn timeline

### 操作步骤

1. **拉 anchor 和目标人物的 LinkedIn profile**（如果 Layer 4 还没做 batch verify，这里一起做）。

2. **对每个目标人物，做时间重叠判定**：

   ```
   Anchor 在组织时间：[start_year] — [end_year]
   目标人入职时间：[join_year]
   
   判定规则：
   - 目标人 join_year > anchor end_year → 不能桥（对方在 anchor 走后才入职）
   - 目标人 join_year ≤ anchor end_year → 可能能桥，看共事时长
   ```

3. **评估共事深度**（决定桥的强度）：

   | 共事关系 | 时长要求 | 桥的强度 | 用法 |
   |---|---|---|---|
   | 同 ExCo / Board | ≥ 3 年 | 强 | 可以直接说"X 建议我联系你" |
   | 上下级 | ≥ 5 年 | 最强 | 关系最深，但要确认不是有矛盾的上下级 |
   | 同部门同级 | ≥ 3 年 | 中 | "我们共同认识 X" |
   | 不同部门 | 任意 | 弱 | 只能说"X 提到过贵组织" |

4. **评估 anchor 衰减度**（R2 规则）：

   | 离任时长 | 衰减程度 | 用法 |
   |---|---|---|
   | ≤ 3 年 | 新鲜 | 可多次 reach，关系还活着 |
   | 4-7 年 | fade 中期 | 1-2 次到位，不能反复用 |
   | 8+ 年 | 基本失效 | 最多 1 次精准 ask，不能当 ongoing 桥 |

   WEF 案例教训：Lee Howell 2017 离任，到 2026 已 9 年。Cathy Li 2018 才入职——Howell 完全不认识 Cathy，不能桥。但 Saadia Zahidi 2005 就在，跟 Howell 同 ExCo 共事 8 年——这条桥价值很高。

5. **输出 Path A / Path B 标注**：

   - **Path A**（独立 cold reach）：没有 anchor 桥，或 anchor 不认识此人
   - **Path B**（anchor 桥）：anchor 认识此人，标注共事时长 + 桥的强度
   - **Path C**（间接）：reach 目标人的下属，让下属推荐。适用于 follower > 20K 的顶层人物

### 产出
- 每个目标人物的 Path A/B/C 标注 + 理由
- Anchor 桥的使用预算（还能用几次）

### 踩坑清单
- LinkedIn timeline 可能有空白期（没填的年份）。空白 ≠ 没工作，可能是隐私设置。不要假设。
- 同名干扰在跨国组织很严重（印度名/中国名/西班牙名）。cross-validate 至少用 公司 + 头像 + 位置 + 角色 四个维度匹配。

---

## Layer 4 · 多源交叉验证（Cross-source Validation）

> 回答的问题：**我掌握的信息，哪些能信、哪些不能信？**

### 为什么这一层是核心
R8 规则：**Verify >> Discover**。不 verify 的 discovery 是债务。这条规则在 WEF v0.1 → v0.2 的重写里被硬验证了一次——可万直接说"关键人你肯定要走领英 API 查啊"，导致整份报告重做。

### 输入
- Layer 1-3 收集的所有人物信息

### 工具：Apify LinkedIn Profile Scraper

```bash
source ~/.secrets/env.sh
curl -s -X POST "https://api.apify.com/v2/acts/harvestapi~linkedin-profile-scraper/run-sync-get-dataset-items?token=$APIFY_TOKEN&timeout=300" \
  -H "Content-Type: application/json" \
  -d '{
    "queries": [
      "https://www.linkedin.com/in/<slug-1>/",
      "https://www.linkedin.com/in/<slug-2>/",
      "https://www.linkedin.com/in/<slug-3>/"
    ]
  }' > verify-batch.json
```

成本：$0.004 × N 人。5 人 ≈ $0.02。**必须先 dry-run 1 条再 batch**。

### 操作步骤

1. **收集所有目标人物的 LinkedIn URL**。来源优先级：
   - 公司官网直接链接（最可靠）
   - Google 搜 "LinkedIn {name} {company}"
   - 行业媒体文章里的链接

2. **Dry-run 1 条测试** → 确认 actor 返回正常 → **Batch verify 全部**。

3. **对每个人检查三项**：

   | 检查项 | 方法 | 不一致时 |
   |---|---|---|
   | 现职位 vs Layer 1 记录 | 比对 title | 以 LinkedIn 为准，更新 org map |
   | 是否还在目标公司 | 看 current company | 标"已离开" + 标新去处 |
   | tenure 多长 | 看 start date | 判断稳定度 |

4. **标注置信度**：

   | 置信度 | 条件 | 能进邮件吗 |
   |---|---|---|
   | **高** | LinkedIn verify + 至少 1 个独立来源确认 | 能 |
   | **中** | 只有公开新闻/公司网页，未 LinkedIn verify | 不能直接引用，只能间接提及 |
   | **低** | 只有二手平台（The Org/RocketReach）或推断 | 不能 |

5. **"找不到"本身是信号**：
   - LinkedIn 完全搜不到 → 对方设了隐私限制 / 不活跃
   - 影响冷联系策略：不要在 cold message 里引用"我看了您的 LinkedIn"
   - 可能需要走 email 而不是 LinkedIn connect

### 产出
- 全部人物的置信度标注（高/中/低）
- 信息修正清单（谁的 title 变了、谁已离开）
- "找不到"清单 + 对策

### 踩坑清单
- **不能用的 actor**（截至 2026-04）：
  - ❌ `harvestapi/linkedin-profile-search-by-name`：返回 0 结果
  - ❌ `apimaestro/linkedin-company-employees-scraper`：bug，返回 Google 员工
  - ✅ 只用 `harvestapi/linkedin-profile-scraper` + 已知 URL
- 4 年以上的 promotion 新闻，约 40% 的人已变动（Deloitte 案例：7 人中 3 人升职/调任/离开）
- Jelle Roebroek 教训：Google snippet 显示"Senior Consultant AI @ Deloitte"，实际已离职去 Mastercard 一年多。**Google snippet ≠ 当前状态**。

---

## Layer 5 · 可达性评估（Reachability Assessment）

> 回答的问题：**每个目标人物，我通过什么路径、用什么方式联系 ta？**

### 输入
- Layer 1-4 的完整汇总：org map + 隐藏关系 + 时间重叠 + 置信度

### 操作步骤

1. **对每个目标人物，判断冷联系难度（R1）**：

   | Follower 量级 | 难度 | 策略 |
   |---|---|---|
   | < 5K | 低 | 直接 LinkedIn connect with note，回复率高 |
   | 5K-20K | 中 | 先在对方公开内容评论区刷 3-5 次高质量 take，再 connect |
   | > 20K | 高 | 不直接 cold reach。走 anchor 桥（Path B）或 reach 其下属让下属推荐（Path C） |

   WEF 案例：Cathy Li 12,263 followers → 中高难度，不直接 cold reach；Devendra Jain 2,687 followers → 低难度，直接 connect。

2. **综合判断 Path**：

   ```
   IF anchor 可用 AND 共事验证通过 → Path B（anchor 桥）
   ELIF follower < 5K → Path A（直接 cold reach）
   ELIF follower 5K-20K → Path A（先评论区预热）
   ELIF follower > 20K → Path C（reach 下属）
   ```

3. **排优先级（R6）**：三个信号综合排序：
   - 信号 a：该业务线历史上出过 ECP/intern track 吗（有招聘心智 = 更容易说动）
   - 信号 b：在招岗位关键词跟 persona 的重合度
   - 信号 c：目标人的职业路径跟 persona 的相似度（路径相似 = 对方更容易产生共鸣）

4. **给每个目标人写 4 件套**：

   | 件 | 内容 | 数据来源 |
   |---|---|---|
   | 为什么是 ta | 具体到 ta 的 hiring 偏好 + 用户对位 | Layer 1 的 fit 分析 |
   | 人物细节 | 教育 / 发言主题 / 帖子趋势 | Layer 2 的帖子分析 + Layer 4 的 verify 数据 |
   | Networking 策略 | Path A/B/C + 时机 + Fallback | Layer 3 + Layer 5 综合 |
   | Cold message 钩子 | 100-150 词草稿 | 必须引用对方近期具体内容（R7 三件套） |

5. **Cold message 三件套检查（R7）**。每封必含：
   - ✅ 引用对方近期具体公开内容（不是空话"我看了您的工作"）
   - ✅ 30 分钟以内的 informational ask
   - ✅ 你能给对方什么互利（practitioner perspective / specific question / market intel）

### 产出
- 优先级排序的目标人物列表
- 每人的 Path + 4 件套 + cold message 草稿
- 全局策略建议（thesis，一段话）

### 踩坑清单
- **不要批量发同样模板 cold message**。每封的"引用近期内容"部分必须不同。
- **不要在公开 HTML 里写 anchor 真名**（generic case）。保护 anchor。
- **不要把 anchor 当招聘中介**。frame 成知识对接，不是"帮我投简历"。
- **推断标置信度**。邮件里只用高置信度信息。WEF 案例：Arun Venkat = "his guys" 这个推论是 70%，可万 challenge 后决定不在邮件里点名。

---

## 关系图谱可视化规范

每次跑完 5 层之后，输出一张关系图谱。规范如下：

### 节点定义

| 节点类型 | 视觉表现 | 大小逻辑 |
|---|---|---|
| 高优先级目标（Priority 1-3） | 实心黑色圆 | 大 |
| 中优先级目标（Priority 4-5） | 深灰圆 | 中 |
| 上下文人物（非目标但在 org chart 上） | 浅灰圆 | 小 |
| 已离开的人 | 虚线边框 + 浅灰填充 | 中 |

### 边的定义

| 边类型 | 视觉表现 | 含义 |
|---|---|---|
| 汇报关系 | 实线 + 箭头 | A 向 B 汇报 |
| 同 capability | 细实线，无箭头 | 同一个业务方向 |
| 帖子发现的同事关系 | 蓝色虚线 | 通过 LinkedIn 帖子发现的协作关系 |
| Industry 横切 | 点线 | 跨 capability 的行业关联 |
| Anchor 桥路径 | 绿色粗线 | 可通过 anchor 联系 |
| 已断开（离职） | 红色虚线 | 人已离开，关系可能仍在 |

### 布局原则
- 按 capability bucket 聚类，同 bucket 的人靠在一起
- 高优先级目标在视觉中心
- Executive Board 在顶部
- 已离开的人在边缘

### 交互（如用 D3.js）
- Hover：显示人物基础信息（name / title / followers / LinkedIn URL / 置信度）
- Click：展开 4 件套面板
- Drag：可重新布局节点
- Zoom/Pan：缩放导航

---

## 附录：数据源可靠度总表

| 数据源 | 能得到什么 | 成本 | 置信度 | 用法 |
|---|---|---|---|---|
| 公司官网 capability page | Contact partner + email | 0 | 高 | Layer 1 首选 |
| 公司官网 leadership page | ExCo / Board | 0 | 高 | Layer 1 |
| 行业媒体 promotion 新闻 | 中层人物 + 晋升时间 | 0 | 中（4 年后 40% 变化） | Layer 1，需 verify |
| LinkedIn profile scraper | 完整 profile + timeline | $0.004/人 | 高 | Layer 4 必做 |
| LinkedIn posts scraper | 帖子 + @提及 + 合作信号 | ~$0.01/人 | 高 | Layer 2 |
| Google search | 多源线索 | 0 | 混合 | 全程辅助 |
| The Org / RocketReach | Org chart 参考 | 0 | 低 | 只当 lead，不当 evidence |
| Workday API actors | 在招岗位 | $0.3-2/次 | 低（反爬严重） | **不推荐** |

---

## 版本记录

| 版本 | 日期 | 变动 |
|---|---|---|
| v1.0 | 2026-05-08 | 从 4 个案例提炼初版 |
