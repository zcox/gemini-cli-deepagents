# Overview

LangChain recently blogged about [deep agents](https://blog.langchain.com/deep-agents/), and built a corresponding [library](https://github.com/hwchase17/deepagents).
Deep agents consist of:
- A detailed system prompt
- Planning tool
- Sub agents
- File system

While [gemini-cli](https://github.com/google-gemini/gemini-cli) is instructed by default to be a coding agent, it can be customized to be some other kind of deep agent.
Let's see how to turn gemini-cli into a general deep agent.

## File System

gemini-cli includes [many useful tools](https://github.com/google-gemini/gemini-cli/blob/main/docs/tools/index.md), especially its file system tools.
So it has this part covered.

## System Prompt

gemini-cli's [default system prompt](https://github.com/google-gemini/gemini-cli/blob/main/packages/core/src/core/prompts.ts) instructs it to be a coding agent, and its system of GEMINI.md files let you customize its coding agent abilities.

However, you can also provide your own system prompt. If you set the env var `GEMINI_SYSTEM_MD=1` then gemini-cli will obtain its system prompt from `.gemini/system.md`.

To see an example:

```sh
cd system_prompt
GEMINI_SYSTEM_MD=1 gemini
```

gemini-cli now talks like a pirate!

## Planning Tool

The LangChain blog post, their deepagents library, and Claude Code all use a todo list tool for planning.

gemini-cli does not appear to use todo lists for planning; instead, its system prompt contains multiple sets of instructions that follow this pattern:
1. understand
2. plan
3. implement
4. verify

While gemini-cli seems to plan just fine with this approach, it was simple enough to implement a basic todo list tool as a [custom tool](https://github.com/google-gemini/gemini-cli/blob/main/docs/core/tools-api.md#extending-with-custom-tools). See `bin/tools/write_todos`. It's initially basically a copy of the LangChain deepagents todos tool. GC is happy to use it when you explicitly talk about todos, but doesn't seem to use it when planning its own work. It could probably be instructed to do so more explicitly via GEMINI.md.

## Sub Agents

gemini-cli is most lacking in this area. While there are [plans to build subagents](https://github.com/google-gemini/gemini-cli/issues/3132), and some [initial progress](https://github.com/google-gemini/gemini-cli/pull/1805), currently gemini-cli does not support subagents directly.

One possible hack might be a custom tool that runs `gemini -p "instructions for subagent"`.

## sqlite3 Tools

While not formally a part of "deep agents", this repo also provides gemini-cli a few custom tools for working with sqlite3, a local SQL database.

If a deep agent has access to a file system, shouldn't it also have access to a SQL database?

GC was also very helpful in actually building these custom tools, using the info in GEMINI.md.

One fun use of sqlite is getting GC to build the DB for a blog system, populate it with data, and then display a simple blog app in the terminal.

## Security

If an agent can do something, it can be tricked into doing it. [Simon Willison’s Lethal Trifecta](https://simonwillison.net/2025/Jun/16/the-lethal-trifecta/) is important to consider for any deep agent.

GC (just like Claude Code and others) has access to your file system and can run shell commands, which pretty much ticks the boxes for private data access and external communication ability. Pay attention to those user confirmation prompts on tool calls. And add MCP servers with care.

What about untrusted content? As soon as you run `gemini`, any of the files it that dir can contain prompt injections. Maybe think twice about running that in some random github repo you cloned (including this one), or use a sandbox. Aside from that, like Simon says:

> Any time you ask an LLM system to summarize a web page, read an email, process a document or even look at an image there’s a chance that the content you are exposing it to might contain additional instructions which cause it to do something you didn’t intend.
