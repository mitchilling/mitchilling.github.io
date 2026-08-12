---
layout: post
title: "CDP：Lynx 运行时的下一接口"
description: "Agent 不仅需要编写代码，也需要运行、检查和验证应用。长期服务于 GUI DevTools 的 CDP，如今也正成为 Agent 的程序化接口。"
date: 2026-08-11 09:00:00 +0800
categories: [lynx, cdp]
lang: zh
translation_key: cdp-runtime-interface
permalink: /zh/posts/cdp-runtime-interface/
extra_styles:
  - /assets/css/posts/cdp-runtime-interface.css
---

当我们让 Agent 修复一个 Lynx 页面时，成功修改代码还不代表任务结束。我们仍希望它能回答：

> **渲染出的 UI 是否按预期发生了变化？**
>
> **交互能否正常工作？**
>
> **这次修改是否引入了错误或回归？**

开发者越来越希望 Agent 能回答这些问题，而不只是提交一份代码变更。这就要求 Agent 能够运行应用、测试相关流程，并根据这些结果判断任务是否真正完成。

这个想法并不新鲜。新的地方在于，完整的验证闭环现在需要以程序化方式开放出来，而不能只依赖由人操作的开发工具。

这里有一个例子。我在通勤路上用 Lynx vibe-code 了一个数独小游戏，权当消遣。随后，我让 Agent 验证游戏流程。它通过 CDP（[Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol)）查找 UI 元素、检查状态、计算触摸坐标、发送输入事件，并确认最终结果。

<figure>
  <img src="{{ '/assets/images/cdp-runtime-interface/cdp_usage_by_agent.png' | relative_url }}" alt="Agent 通过 CDP 验证流程">
  <figcaption>一份基于 CDP 的 Lynx 数独流程验证报告。</figcaption>
</figure>

## 为什么 CDP 在 AI 时代更重要

### AI 原生开发需要运行时访问

回到开头的问题：Agent 如何检查和控制 Lynx 运行时？

Computer Use 已经很流行，也切实可行；我自己在开发中也使用它。它尤其适合探索 UI 或验证一段较短的流程。

但随着任务变得更复杂，这种探索的代价会迅速上升，消耗更多时间和 token。一个合理的解释是：在视觉交互中，Agent 需要从看到的内容中*推断*意图。

相比之下，一套文档完善的运行时接口能让 Agent 更直接地发现支持哪些操作，以及这些操作会返回什么结果。在 Lynx 中，CDP 就承担着这一角色。

### 人、Agent 与自动化共用一个接口

尽管 Agent 让许多任务更高效，图形交互界面（GUI）的开发工具对人类开发者仍然不可或缺。当 Agent 的结果不尽如人意时，我们就需要接手任务。

因此，Lynx 运行时必须同时服务 GUI 工具、Agent 和自动化。我们不希望它们各自定义对运行时行为的理解。

这正是 CDP 发挥作用之处。

<figure class="protocol-figure">
  <div class="protocol-diagram" role="img" aria-label="GUI DevTools、Agent CLI 和自动化脚本通过 CDP 与 Lynx DebugRouter 通信，由它将消息桥接到 Lynx 运行时。">
    <div class="protocol-tools">
      <span>GUI DevTools</span>
      <span>Agent CLI</span>
      <span>自动化脚本</span>
    </div>
    <div class="protocol-arrow"><span>CDP</span></div>
    <div class="protocol-router"><strong>Lynx DebugRouter</strong><small>共享接口</small></div>
    <div class="protocol-arrow"><span>桥接消息</span></div>
    <div class="protocol-runtime">Lynx 运行时</div>
  </div>
  <figcaption>CDP 是共享接口。</figcaption>
</figure>

### CDP 是坚实的基础

我们来看看 CDP 提供了什么。

<div class="pillars" role="list">
  <div class="pillar" role="listitem">
    <span class="pillar-number">01</span>
    <span class="pillar-title">现有生态</span>
    <p>许多工具已经能够使用 CDP，因此 Lynx 可以融入现有生态，而不必再让每个工具学习一套新协议。</p>
  </div>
  <div class="pillar" role="listitem">
    <span class="pillar-number">02</span>
    <span class="pillar-title">可移植的消息</span>
    <p>JSON 消息可用于桌面端、移动端及其他环境，而无需将工具绑定到某个 GUI 或特定编程语言的 API。</p>
  </div>
  <div class="pillar" role="listitem">
    <span class="pillar-number">03</span>
    <span class="pillar-title">通用的词汇</span>
    <p>标准领域（Domain）覆盖常见的工具需求，例如 Debugger 和 Profiler；Lynx 扩展则暴露运行时特有能力。</p>
  </div>
</div>

作为受 Web 启发的框架，Lynx 自早期便采用了 CDP。Lynx DevTool 多年来一直基于标准 CDP 领域构建，并针对 Lynx 运行时特有能力扩展了协议。我们很高兴看到，这个长期服务于 GUI DevTool 的基础，如今也成为 Agent 的程序化接口。

在与 Agent 协作数月、完成许多开发和验证任务之后，我认为现在可以自信地说：

**CDP 是观测和控制 Lynx 运行时的下一个接口。**

## 现在已经有什么

这套方法已经体现在现有工具中：

- [Lynx DevTool](https://github.com/lynx-family/lynx-devtool) 为桌面端开发者提供 GUI。
- [Lynx DevTool skill](https://lynxjs.org/ai/skills/lynx-devtool) 和 [Lynx DevTool MCP](https://lynxjs.org/zh/ai/lynx-devtool-mcp) 让 Agent 能够观测和控制 Lynx 页面。
- [DebugRouter](https://github.com/lynx-family/debug-router) 负责建立连接，并将 CDP 消息桥接到运行时。

仍在完善中的 [Lynx CDP API Reference](https://lynxjs.org/next/zh/api/cdp/api-ref.html) 展示了当前提供的能力：

<div class="capability-table" role="region" aria-label="当前 Lynx CDP API 能力" tabindex="0">
  <table>
    <thead>
      <tr>
        <th>标准 CDP 领域</th>
        <th>Lynx 扩展</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>DOM、CSS、Debugger、Runtime、Performance 和 Tracing</td>
        <td>组件、UI 树、模板、录制、回放及其他运行时特有能力</td>
      </tr>
    </tbody>
  </table>
</div>

这是一份仍在演进的实验性参考：它描述了 Lynx 当前暴露的 CDP 能力，但还不是一份完整的规范。它还没有涵盖事件（Event），部分 Lynx 扩展也仍需要更完善的文档。不过，我相信我们已经有了一个好的开始。

## 接下来会做什么

团队将在以下方向继续推进：

- **扩展标准 CDP 领域**，包括 `Input`、`Network`、`Animation` 等。
- **为运行时能力增加 Lynx 特有扩展**，例如 `NativeModule`。
- **完善协议契约**，补齐事件、标准化的响应结构和一致的错误行为。
- **在 CDP 之上构建 MCP 集成和 Agent 技能**，让 Agent 更容易发现和使用 Lynx 运行时能力。

我们将继续在这套共享接口上构建工具。如果你希望参与共建，我们非常欢迎你来一起贡献。
