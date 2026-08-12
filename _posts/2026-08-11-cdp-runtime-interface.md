---
layout: post
title: "CDP: The Next Interface for the Lynx Runtime"
description: "Agents must do more than write code: they need to run, inspect, and verify applications. CDP, long used for GUI DevTools, is now the programmatic interface for agents too."
date: 2026-08-11 09:00:00 +0800
categories: [lynx, cdp]
lang: en
translation_key: cdp-runtime-interface
permalink: /posts/cdp-runtime-interface/
extra_styles:
  - /assets/css/posts/cdp-runtime-interface.css
---

If we ask an agent to fix a Lynx page, a successful code edit should not be the end of the task. We still want it to answer:

> **Did the rendered UI change as intended?**
>
> **Does the interaction work?**
>
> **Did the change introduce an error or regression?**

Developers increasingly expect agents to answer those questions, not just produce a diff. That requires the agent to run the application and test the relevant flow to see whether the task is actually complete.

This idea is not new, of course. What's new is that the full verification loop now needs to be accessible programmatically, not only through a human-operated development tool.

Here is an example. I vibe-coded a small Sudoku game with Lynx to pass the time on my commute to the office. Then I asked an agent to verify the gameplay flow. It used CDP ([Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol)) to find UI elements, inspect the state, calculate touch coordinates, send input, and confirm the outcome.

<figure>
  <img src="{{ '/assets/images/cdp-runtime-interface/cdp_usage_by_agent.png' | relative_url }}" alt="An agent verifies a flow through CDP">
  <figcaption>A CDP-based verification report for a Lynx Sudoku flow.</figcaption>
</figure>

## Why CDP matters more in the AI era

### AI-native development needs runtime access

Let's return to the questions from the beginning: how can agents inspect and control the Lynx runtime?

Well, computer use is popular and feasible; I have used it in my own development work. It can work especially well for exploring a UI or verifying a short flow.

However, as tasks become more involved, that exploration quickly becomes more expensive, both in time and tokens. A plausible explanation is that during visual interaction, an agent is expected to *infer* intent from what it sees.

On the other hand, a documented runtime interface gives the agent a more direct way to discover supported operations and their results. In Lynx, CDP plays that role.

### One interface for people, agents, and automation

Although agents make many tasks more efficient, GUI DevTools remain essential for people. When an agent's result is unsatisfactory, we take over.

Therefore, the Lynx runtime must serve GUI tooling, agents, and automation at the same time. We do not want each of them to define its own view of runtime behavior.

That's where CDP shines.

<figure class="protocol-figure">
  <div class="protocol-diagram" role="img" aria-label="GUI DevTools, agent CLIs, and automation scripts use CDP to communicate with the Lynx DebugRouter, which bridges messages to the Lynx runtime.">
    <div class="protocol-tools">
      <span>GUI DevTools</span>
      <span>Agent CLIs</span>
      <span>Automation scripts</span>
    </div>
    <div class="protocol-arrow"><span>CDP</span></div>
    <div class="protocol-router"><strong>Lynx DebugRouter</strong><small>shared interface</small></div>
    <div class="protocol-arrow"><span>bridged messages</span></div>
    <div class="protocol-runtime">Lynx Runtime</div>
  </div>
  <figcaption>CDP is the shared interface.</figcaption>
</figure>

### CDP is a strong foundation

Let's take a look at what CDP offers.

<div class="pillars" role="list">
  <div class="pillar" role="listitem">
    <span class="pillar-number">01</span>
    <span class="pillar-title">Existing ecosystem</span>
    <p>Tools already speak CDP, so Lynx can participate rather than introduce a new protocol every tool must learn.</p>
  </div>
  <div class="pillar" role="listitem">
    <span class="pillar-number">02</span>
    <span class="pillar-title">Portable messages</span>
    <p>JSON-based messages work across desktop, mobile, and other environments without coupling tools to a GUI or language-specific API.</p>
  </div>
  <div class="pillar" role="listitem">
    <span class="pillar-number">03</span>
    <span class="pillar-title">Common vocabulary</span>
    <p>Standard domains cover common tooling needs, like Debugger and Profiler, while explicit Lynx extensions expose runtime-specific capabilities.</p>
  </div>
</div>

As a web-inspired framework, Lynx adopted CDP early. The DevTool has built on standard CDP domains for years, then extended the protocol for Lynx-specific runtime capabilities. We are happy to see the foundation long used by GUI DevTool become the programmatic interface for agents too.

After months of co-working with agents on development and verification tasks, I believe it is now safe to say:

**CDP is the next interface for observing and controlling the Lynx runtime.**

## What is here today

This approach is already visible in the tools:

- [Lynx DevTool](https://github.com/lynx-family/lynx-devtool) offers a GUI for desktop developers.
- The [Lynx DevTool skill](https://lynxjs.org/ai/skills/lynx-devtool) and [Lynx DevTool MCP](https://lynxjs.org/ai/lynx-devtool-mcp) enable agents to observe and control Lynx pages.
- [DebugRouter](https://github.com/lynx-family/debug-router) establishes the connections and bridges CDP messages to the runtime.

The working [Lynx CDP API Reference](https://lynxjs.org/next/api/cdp/api-ref) shows the current surface:

<div class="capability-table" role="region" aria-label="Current Lynx CDP API surface" tabindex="0">
  <table>
    <thead>
      <tr>
        <th>Standard CDP domains</th>
        <th>Lynx extensions</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>DOM, CSS, Debugger, Runtime, Performance, and Tracing</td>
        <td>Components, UI tree, templates, recording, replay, and other runtime-specific capabilities</td>
      </tr>
    </tbody>
  </table>
</div>

It is an experimental working reference: it describes the CDP surface Lynx exposes today, but it is not yet a complete specification. Events are not covered yet, and some Lynx extensions need deeper documentation. Still, I believe we are off to a good start.

## What comes next

We are continuing work in these areas:

- **Expand standard CDP domains**, including `Input`, `Network`, `Animation` and so on.
- **Add Lynx-specific extensions** for runtime capabilities, for example `NativeModule`.
- **Complete the protocol contract** with events, standard response shapes, and consistent error behavior.
- **Build MCP integrations and skills** on top of CDP so agents can discover and use Lynx runtime capabilities more easily.

We will keep building tooling on this shared interface. If you want to help shape it, we welcome your contributions.
