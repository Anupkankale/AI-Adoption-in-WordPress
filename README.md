# AI Adoption in WordPress

Talk given at the **WordPress Mumbai Meetup**, 12 September 2026, hosted by Paytm in Andheri East.

> WordPress did not ship AI. It shipped the plumbing so that you can.

This repo has the slides, the demo code, and a runbook you can follow to connect your own WordPress site to Claude or ChatGPT.

---

## What the talk argues

Most "AI in WordPress" content is either hype or a plugin advert. This talk is about **adoption** — the difference between shipping a button and having a workflow that actually depends on something.

**Usage** is "we added AI." **Adoption** is "a workflow now depends on it" — with a named owner, a known cost, a known failure mode, and a rollback.

Three tests for whether you've adopted something or just used it:

1. Would anyone notice if it stopped working tomorrow?
2. Does the workflow still hold when the model is wrong?
3. Can it run without you in the room to explain it?

### The adoption ladder

| Rung | What it is | What you need |
|------|-----------|---------------|
| **4** | Agent access | MCP adapter, trust threshold, approvals |
| **3** | Registered capability | Abilities with schemas and permission callbacks |
| **2** | Configured provider | Connectors, stored keys, a named cost owner |
| **1** | Assisted authoring | A human approves every output |
| 0 | Ad hoc chatbot use | *Not adoption* |

**The rule:** never move a workflow up a rung until you can afford it being wrong at the rung it's on.

Most failed AI projects skipped from rung 1 straight to rung 4.

---

## Connector vs MCP

The most confused pair of words in this space. They point in opposite directions:

- **Connector** — your site talks *out* to a model provider. Your site is the client.
- **MCP** — an outside agent talks *in* to your site. Your site is the server.

**A connector gives your site a brain. MCP gives someone else's brain hands on your site.**

Different risk conversations entirely. A connector is a procurement question; MCP is a security question.

---

## Why not to start at MCP

MCP is a transport. It grants your site no new capability — it exposes capabilities you already registered as abilities.

- **No abilities registered?** MCP gives an agent nothing. A door into an empty room.
- **Sloppy permission callbacks?** MCP gives an agent everything. A door into the whole building.

Either way the work is at rung 3. People who start at MCP are building a demo; people who start at abilities are building adoption.

---

## What's in this repo

```
├── slides/
│   └── ai-adoption-in-wordpress.pptx
├── demo/
│   ├── RUNBOOK.md              # connect your site to Claude, step by step
│   └── mu-plugins/
│       └── demo-ability.php    # one read-only ability, marked public
└── README.md
```

---

## Try the demo yourself

Full instructions in [`demo/RUNBOOK.md`](demo/RUNBOOK.md). The short version:

1. Install the **MCP Adapter** plugin (use the built release asset, not the source ZIP)
2. Create a **dedicated Editor user** — never use your admin account
3. Generate an **application password** for that user
4. Drop `demo-ability.php` into `wp-content/mu-plugins/`
5. Point Claude Code or Claude Desktop at your site's MCP endpoint
6. Run the three prompts below

> ⚠️ **Staging or local sites only.** Every method here gives write access to a real database.

### The three prompts

**1. Discovery**
> What can you do on this WordPress site? List every tool you have, and mark which ones can change something.

The agent reads a self-describing capability list. Compare with REST, which exposes endpoints for someone who already knows what to call.

**2. Read**
> List my last 10 published posts and tell me which ones are missing an excerpt.

Visibly useful, zero risk. Nothing is written.

**3. The wall**
> Now delete the oldest one.

It can't. No delete ability was registered, so there's no tool to call.

**Nobody trusted the model there. The model was simply never given the option.** That's the whole talk in one interaction.

---

## The state of play

*Accurate as of September 2026 — check the sources below, some of this will have moved.*

| Version | When | What landed |
|---------|------|-------------|
| 6.9 | Dec 2025 | Abilities API in core |
| 7.0 | May 2026 | AI Client, Connectors hub, Command Palette |
| 7.1 | Aug 2026 | Shipped at WordCamp US — embeddings did **not** make it |
| 7.2 | Beta ~20 Oct 2026 | Embeddings, Secrets API, CRUD abilities pattern |

### The four AI Building Blocks

Announced by the WordPress AI Team in July 2025:

- **PHP AI Client SDK** — one interface to any provider's models
- **Abilities API** — a central registry that makes WordPress discoverable
- **MCP Adapter** — translates abilities into MCP tools
- **AI plugin** — the laboratory and reference implementation

Distribution philosophy: canonical first, core when ready.

**Common misunderstanding worth clearing up:** AI features ship via a separate plugin, not in core. Core shipped the building blocks.

---

## Two things developers should take away

**`permission_callback` is not optional.** An ability without a real one is a public API you didn't know you published.

**Abilities are primitives, not transport.** REST, MCP and the command palette are all just ways of *reaching* an ability. The AI Team made this an explicit design decision — which means your abilities survive the protocol of the month.

---

## The question nobody asks

**What would you turn off, and who turns it off?**

A real answer has three parts: a named person (not "the team"), a written trigger (cost, error rate, a kind of complaint), and a switch you have actually flipped at least once.

Almost nobody writes this down. It's the difference between adoption and enthusiasm.

---

## Sources

Everything factual in this talk came from these, not from hot takes:

- [make.wordpress.org/ai](https://make.wordpress.org/ai/) — the AI Team's home. Weekly summaries, roadmap, decisions. **Start here.**
- [AI Building Blocks for WordPress](https://make.wordpress.org/ai/2025/07/17/ai-building-blocks/) — the July 2025 post that set the direction
- [developer.wordpress.org/abilities-api](https://developer.wordpress.org/) — the Abilities API handbook
- [wordpress.org/plugins/ai](https://wordpress.org/plugins/ai/) — the canonical AI plugin
- [github.com/WordPress/mcp-adapter](https://github.com/WordPress/mcp-adapter) — the MCP Adapter
- [github.com/WordPress/php-ai-client](https://github.com/WordPress/php-ai-client) — the provider-agnostic PHP SDK

---

## Get involved

This isn't a product to wait for — it's a project you can join, and it needs people.

The team is looking for maintainers for the PHP AI Client, the WordPress AI Client and the connector plugins, plus help with testing, triage, translation, design, accessibility and docs.

- **Slack:** `#core-ai` on WordPress Slack
- **Office hours:** alternating Thursdays, 17:00 UTC (**10:30 PM IST**)

---

## Questions from the talk

**Will AI replace WordPress developers?**
Someone still has to decide which abilities exist, who may call them, and what happens when the model is wrong. That job didn't get smaller — it got more consequential.

**Client data going to a US API — what about the DPDP Act?**
Connectors mean the site owner picks the provider, so it's a configuration decision rather than something baked into your code. For data that can't leave your infrastructure, look at local models via Ollama. Do your own compliance homework — this isn't legal advice.

**Is the Abilities API actually adopted, or half-built?**
The scepticism is real and it's in the project's own threads. The API is in core, the AI plugin is the reference implementation, and the CRUD pattern is targeted for 7.2. Whether it becomes load-bearing depends on plugin authors using it.

**What about GPL and AI-generated code?**
Unsettled. The project is drafting AI contribution guidelines meant to apply project-wide — that's the thing to watch.

---

## Licence

Slides and docs: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)
Code: GPL-2.0-or-later, matching WordPress

---

*Talk by Anup Kankale · [@Anupkankale](https://github.com/Anupkankale). Corrections welcome — open an issue.*
