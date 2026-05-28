---
title: Agent Skill
sidebarTitle: Agent Skill
description: Install the Langfuse agent skill to give your coding agent access to Langfuse tracing, prompt management, datasets, and documentation — right from the editor.
---

# Langfuse Agent Skill

The Langfuse agent skill helps AI coding agents use Langfuse effectively. It follows the open [Agent Skills](https://github.com/anthropics/skills) standard and works with Claude Code, Cursor, Windsurf, and other compatible agents. The skill is open source on [GitHub](https://github.com/langfuse/skills).

## Why use it [#why-use-it]

Coding agents produce significantly better results with the skill installed, because they are conditioned to follow best practices. A coding agent with access to the skill has an opinionated view on what good looks like, based on Langfuse's knowledge.

The skill is a self-contained folder with a `SKILL.md` entrypoint describing general rules and instructions on using documentation, plus reference docs for specific workflows that are filled with specific best practices:

<div>
  <FileTree>
    <FileTree.File name="SKILL.md" />
    <FileTree.Folder name="references" defaultOpen>
      <FileTree.File name="cli.md" />
      <FileTree.File name="instrumentation.md" />
      <FileTree.File name="prompt-migration.md" />
      <FileTree.File name="..." />
    </FileTree.Folder>
  </FileTree>
</div>

The skill uses a progressive disclosure model: the frontmatter is always loaded into the agent's context so it knows when the skill is relevant, but the full instructions and reference docs are only loaded on demand. This keeps context usage low while giving agents access to specialized knowledge.

## Install [#install]

Install the [Langfuse AI Skill](https://github.com/langfuse/skills) to let your coding agent access all Langfuse features.

<Tabs items={["Ask your coding agent", "Cursor plugin", "Manual installation"]}>

<Tab>

Ask your coding agent to install the skill by pointing to the [GitHub repository](https://github.com/langfuse/skills).

```txt filename="Agent instruction"
"Install the Langfuse AI skill from github.com/langfuse/skills."
```

</Tab>

<Tab>

Langfuse has a [Cursor Plugin](https://cursor.com/docs/plugins) that includes the skill automatically.

<div className="flex gap-2 mt-3 mb-6">
  <Button asChild>
    <Link
      href="https://cursor.com/marketplace/langfuse"
      target="_blank"
      rel="noopener noreferrer"
    >
      Install Plugin in Cursor
    </Link>
  </Button>
</div>
</Tab>

<Tab>

Install via npm ([skills CLI](https://www.npmjs.com/package/skills)):

```bash
npx skills add langfuse/skills --skill "langfuse"
```

If you want to target a specific agent directly:

```bash
npx skills add langfuse/skills --skill "langfuse" --agent "<agent-id>"
```

<details>
<summary>Alternatively you can manually clone the skill</summary>

1. Clone repo somewhere stable

```bash
git clone https://github.com/langfuse/skills.git /path/to/langfuse-skills
```

2. Make sure your agent's skills dir exists

```bash
mkdir -p /path/to/<agent-skill-root>/skills
```

3. Symlink the skill folder

```bash
ln -s /path/to/langfuse-skills/skills/langfuse /path/to/<agent-skill-root>/skills/langfuse
```

</details>

</Tab>

</Tabs>

Once installed, you can prompt your agent with what you want to do. A couple of examples:

- _Show me the last 10 traces with a score below 0.5_
- _Create a dataset called "edge-cases" and add these 3 items to it_
- _Migrate the system prompt in src/agent.ts to Langfuse prompt management_

## Resources [#resources]

- [Langfuse Skills on GitHub](https://github.com/langfuse/skills)
- [Langfuse CLI](/docs/api-and-data-platform/features/cli) — the CLI the skill uses under the hood
- [MCP Server](/docs/api-and-data-platform/features/mcp-server) — alternative protocol-based approach for agents
- [Making Agents fall in love with Langfuse](/blog/2026-02-13-will-you-be-my-cli) — the full story behind the skill, CLI, and agent platform
- [Using Agent Skills to Improve your Prompts](/blog/2026-02-16-prompt-improvement-claude-skills)
- [Evaluating AI Agent Skills](/blog/2026-02-26-evaluate-ai-agent-skills)
