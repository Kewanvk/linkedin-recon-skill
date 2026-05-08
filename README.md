# LinkedIn Recon

AI 驱动的招聘决策人反查工具。给一个岗位链接，返回真正的 hiring manager、组织架构、联系策略和定制化联系信。

## 做什么

1. **从岗位反查决策人** — 不是 HR，是真正拍板的人
2. **画组织架构** — 向上（汇报对象）、横向（同级）、向下（团队），SVG 可视化
3. **深度分析每个目标人** — LinkedIn 帖子、职业路径、公开发言、评论区关系网络
4. **输出一份情报报告** — 交互式关系图谱 + 个性化联系信草稿 + 行动时间线

基于 Claude (Anthropic) + LinkedIn API (Apify) + D3.js 构建。

## 报告长什么样

单页 HTML，包含六个 section：

| Section | 内容 |
|---|---|
| Hero 封面 | "Prepared for [用户名]" + 三个反直觉大数字 |
| 你不知道的 N 件事 | 可展开的 Insight Cards，每张一个反直觉发现 |
| 决策人地图 | SVG 组织架构图，只画相关人 |
| 隐藏关系网络 | D3.js 力导向图，官方架构之外的暗线 |
| 你的 N 条路径 | 联系目标 + 暗线情报（高管偏好画像） |
| 作战计划 | 匹配度分析（视觉卡片）+ 行动时间线 |

报告设计规范 v1.0，基于 2026-05-08 SWISS KRONO case 定稿。

## 安装

### Claude Code

```bash
cp recon.md ~/.claude/commands/recon.md
```

在 Claude Code 里输入 `/recon` 即可。

### Codex

把 `recon.md` 复制到项目的 agent instructions 目录，在 `AGENTS.md` 里引用。

## 前置条件

- **Apify 账号**（免费 tier 即可，$5/月额度）— 首次运行时 skill 会引导配置
- **Claude Code** 或 **Codex** 环境

## 文件

| 文件 | 用途 |
|---|---|
| `recon.md` | Skill 主文件（安装这个） |
| `linkedin-network-mapping-sop.md` | 三步工作流总览 |
| `relationship-mapping-sop.md` | 五层方法论详解 |
| `process-constraints.md` | 文件命名、渐进暴露规则 |

## 成本

每次调查 < $0.05（LinkedIn API 调用费）。完整流程约 30 分钟。

## License

MIT
