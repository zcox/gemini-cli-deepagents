# Overview

LangChain recently blogged about [deep agents](https://blog.langchain.com/deep-agents/), and built a corresponding [library](https://github.com/hwchase17/deepagents).
Deep agents consist of:
- A detailed system prompt
- Planning tool
- Sub agents
- File system

While [gemini-cli](https://github.com/google-gemini/gemini-cli) is instructed by default to be a coding agent, it can be customized to be some other kind of deep agent.
Let's see how to turn gemini-cli into a deep agent.

## File System

gemini-cli includes [many useful tools](https://github.com/google-gemini/gemini-cli/blob/main/docs/tools/index.md), especially its file system tools.
So it has this part covered.

## System Prompt

gemini-cli's default system prompt instructs it to be a coding agent, and its system of GEMINI.md files let you customize its coding agent abilities.

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

While gemini-cli seems to plan just fine with this approach, it should be simple enough to implement a basic todo list tool as a [custom tool](https://github.com/google-gemini/gemini-cli/blob/main/docs/core/tools-api.md#extending-with-custom-tools).

## Sub Agents

gemini-cli is most lacking in this area. While there are [plans to build subagents](https://github.com/google-gemini/gemini-cli/issues/3132), currently gemini-cli does not support subagents directly.

One possible hack might be a custom tool that runs `gemini -p "instructions for subagent"`.
