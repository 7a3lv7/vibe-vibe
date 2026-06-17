---
layout: home
hero:
  name: "Vibe Vibe"
  text: "人人都能 AI 创造"
  tagline: "不止写代码，更能把想法变成作品\n从首个原型到正式上线，放大你的天才创意"
  image:
    src: /logo.png
    alt: Vibe Coding
  actions:
    - theme: brand
      text: 零基础入门
      link: /Basic/
    - theme: alt
      text: 有基础进阶
      link: /Advanced/
    - theme: alt
      text: 动手做项目
      link: /Practice/
    - theme: alt
      text: 优质好文章
      link: /Articles/

features:
  - title: 零基础友好
    details: 不需要任何编程经验，从一个想法开始，手把手带你把第一个作品真正做出来
  - title: AI 创造工作流
    details: 学会用 AI 帮你梳理想法、搭原型、补内容、改交互、推上线，而不只是把它当成写代码工具
  - title: MVP 思维
    details: 掌握"最小可行产品"理念，用最少的时间验证你的想法，避免功能蔓延的陷阱
  - title: 从作品到产品
    details: 先把东西做出来，再逐步进入全栈架构、部署上线和工程化实践，学习曲线更自然
  - title: 安全意识
    details: 从第一天就建立安全底线意识，学会保护用户数据和避免常见安全漏洞
  - title: 渐进式学习
    details: 基础篇先帮你完成第一个作品，进阶篇再带你把作品推进成更完整的产品
---

<style>
/* 按钮颜色配置 - 针对每个 action 包裹层单独配置 */
/* 第1个按钮：零基础入门 - 绿色 */
.VPHero .actions > .action:nth-child(1) .VPButton {
  background-color: #15A051 !important;
  border-color: #15A051 !important;
}
.VPHero .actions > .action:nth-child(1) .VPButton:hover {
  background-color: #4F5B53 !important;
  border-color: #4F5B53 !important;
}

/* 第2个按钮：有基础进阶 */
.VPHero .actions > .action:nth-child(2) .VPButton {
  background-color: #45523E !important;
  border-color: #45523E !important;
  color: white !important;
}
.VPHero .actions > .action:nth-child(2) .VPButton:hover {
  background-color: #45523E !important;
  border-color: #45523E !important;
}

/* 第3个按钮：动手做项目 - 灰色 */
.VPHero .actions > .action:nth-child(3) .VPButton {
  background-color: #6B7280 !important;
  border-color: #6B7280 !important;
  color: white !important;
}
.VPHero .actions > .action:nth-child(3) .VPButton:hover {
  background-color: #9CA3AF !important;
  border-color: #9CA3AF !important;
}

/* 第4个按钮：优质好文章 - 灰色 */
.VPHero .actions > .action:nth-child(4) .VPButton {
  background-color: #6B7280 !important;
  border-color: #6B7280 !important;
  color: white !important;
}
.VPHero .actions > .action:nth-child(4) .VPButton:hover {
  background-color: #9CA3AF !important;
  border-color: #9CA3AF !important;
}
</style>

## 学习路径

<div class="paths-container" style="display: flex; gap: 2rem; margin: 2rem 0; flex-wrap: wrap;">

<div style="flex: 1; min-width: 300px; padding: 1.5rem; border-radius: 12px; background: linear-gradient(135deg, #15A05111 0%, #15A05122 100%); border: 1px solid #15A05133;">

### 基础篇：从想法到第一个作品

<a href="/Basic/" style="display: inline-block; margin-bottom: 1rem; padding: 0.5rem 1.5rem; background: #15A051; color: white; text-decoration: none; border-radius: 8px; font-weight: 500;">开始阅读基础篇 →</a>

**适合人群**：从未写过代码的小白、文科生、设计师、产品经理

**学习目标**：理解 AI 创造工作流 · 用 AI 做出第一个作品 · 掌握 MVP 思维

**内容预览**：
- **觉醒**：从码农到指挥官的思维转变
- **心法**：MVP 思维、不加功能的艺术
- **技法**：提示词工程、PRD 编写
- **实战**：从 0 到 1 做出你的作品
- **进阶**：版本控制、部署、安全意识

**技术栈**：HTML/CSS/JS · AI 工具（ChatGPT/Claude/Cursor）· Git · 静态部署

</div>

<div style="flex: 1; min-width: 300px; padding: 1.5rem; border-radius: 12px; background: linear-gradient(135deg, #45523E11 0%, #45523E22 100%); border: 1px solid #45523E33;">

### 进阶篇：从作品到产品

<a href="/Advanced/" style="display: inline-block; margin-bottom: 1rem; padding: 0.5rem 1.5rem; background: #45523E; color: white; text-decoration: none; border-radius: 8px; font-weight: 500;">开始阅读进阶篇 →</a>

**适合人群**：想了解完整项目交付流程的开发者

**学习目标**：全栈架构 · 生产级部署 · 协作迭代 · 工程判断力

**内容预览**（已完成2/16，其余章节可阅读序言）：
- **开发基础**：环境搭建 · 开发工具 · AI 调教 · 文档驱动
- **界面与数据**：UI/UX · 环境变量 · 数据库 · 测试自动化
- **部署运维**：公网访问 · Git 协作 · CI/CD · 域名 DNS · VPS 部署
- **安全增长**：SEO · 数据统计 · 用户反馈与产品迭代

**技术栈**：Next.js 16 · React · TypeScript · Tailwind · shadcn/ui · Drizzle · PostgreSQL

</div>

</div>

## 项目状态

::: warning 内部预览版本
- 当前版本为**内部预览版**，不代表最终品质
- 章节内容正在持续优化完善中
- 如发现问题欢迎通过 [GitHub Issues](https://github.com/datawhalechina/vibe-vibe/issues) 反馈
:::

::: tip 🎯 Claude Code 全特性速览
想全面了解 Claude Code 有哪些能力？访问 **[cclog.vibevibe.cn](https://cclog.vibevibe.cn)** 查看从 v0.2 到 v2.1 的完整功能梳理，涵盖 220+ 个版本、1000+ 项更新，包括 AI 模型、多 Agent 协作、MCP 生态、终端体验、安全沙箱等所有核心能力。
:::

::: tip 在线 IDE + Skills
我们推出**在线开发环境**，无需本地配置环境，打开浏览器即可开始学习：

- **云端 IDE** —— 内置完整的 Node.js 24、Python、Docker 等开发环境
- **50+ AI Skills** —— 预装丰富的 AI 辅助技能包，覆盖开发全流程
- **开箱即用** —— 无需安装任何软件，登录即可开始编程实践

让学习门槛降到最低！
:::

## 什么是 Vibe Coding？

> "There's a new kind of coding I call 'vibe coding', where you fully give in to the vibes, embrace exponentials, and forget that the code even exists."
> 
> — Andrej Karpathy, 2025

**Vibe Coding** 是 2025 年提出的最有代表性的 AI 原生构建方式之一，也是 Collins 词典年度词汇。在 Vibe Vibe 里，我们把它放在一个更大的定位里理解：**AI 创造**。

- **用自然语言描述目标与需求**，而不是所有步骤都亲手完成
- **让 AI 参与实现、组织和迭代**，你负责验收、判断和调整
- **更快把想法变成真实成果**，因为做出东西往往比空想和完美规划更能推动学习

简单说：**你负责方向、判断和审美，AI 帮你把作品做出来。**

## 这门课不只是教你学 AI 编程

这门课不是为了把你一次训练成资深工程师。

你不会突然精通算法、框架源码，或者一下获得大厂面试通关能力。

但你会获得：

- 把想法做成真实作品的能力
- 一套可重复使用的 AI 协作方法
- 更强的产品判断、迭代习惯和上线信心

这和传统程序员成长路线不是一回事，但它同样非常有价值，而且更贴近很多普通人真正想解决的问题。

## 适合谁学习？

| 你是谁 | 推荐路径 | 理由 |
|--------|----------|------|
| 设计师 / 产品经理 | 基础篇 | 零代码基础也能做出可运行的原型 |
| 文科生 / 跨专业 | 基础篇 | 从最基础的概念开始，循序渐进 |
| 前端开发者 | 进阶篇 | 扩展后端能力，成为全栈工程师 |
| 后端开发者 | 进阶篇 | 了解现代前端生态和 Next.js |
| 创业者 / 独立开发者 | 两者皆可 | 快速搭建 MVP，独立完成产品 |
| 想提升效率的开发者 | 基础篇 + 进阶篇 | 系统学习 AI 辅助开发工作流 |


