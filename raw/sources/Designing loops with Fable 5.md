---
title: "Designing loops with Fable 5"
source: "https://x.com/rlancemartin/status/2064397389189071163?s=46"
author:
  - "[[@rlancemartin]]"
published: 2026-06-10
created: 2026-06-10
description: "Mythos-class models like Claude Fable 5 have changed the way many of us work at Anthropic. I want to share two tips for getting the most out..."
tags:
  - "clippings"
---
![图像](https://pbs.twimg.com/media/HKYnS0Za8AA_BoV?format=jpg&name=large)

Mythos-class models like Claude Fable 5 have changed the way many of us work at Anthropic. I want to share two tips for getting the most out of this class of models.

**Self-correction loops**

There’s been a lot of interest in loops recently. [@bcherny](https://x.com/@bcherny) [has mentioned](https://x.com/sairahul1/status/2064279904989147577?s=20) that “(his) job is to write loops.” Letting models hillclimb on an evaluation is a common recipe for improving task performance: [/goal](https://code.claude.com/docs/en/goal) in Claude Code and [Outcomes](https://platform.claude.com/docs/en/managed-agents/define-outcomes) in Claude Managed Agent are primitives that let you apply this general recipe for your specific task.

As mentioned in our [prompting guide](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-fable-5), Fable 5 is good at self-correcting in a loop. A well designed goal or rubric adds feedback to the environment that Claude is running in. This let’s Claude run, collect feedback via the goal or rubric, self-correct, and proceed until the goal or rubric is satisfied.

![图像](https://pbs.twimg.com/media/HKYoS3maMAoXYHR?format=jpg&name=large)

I’ll share one toy example that I used to test Fable: [Parameter Golf](https://github.com/openai/parameter-golf) is an open source ML engineering challenge to train the best model that fits in a 16MB artifact in < 10 minutes on 8xH100s.

It’s a bit like [@karpathy](https://x.com/@karpathy)'s [autoresearch](https://github.com/karpathy/autoresearch) project: it tests the ability of an agent to edit basic training code (a single train\_gpt.py file), launch training, poll the log, read the score, and decide what experiment to run next.

I compared Fable 5 to Opus 4.7 on this challenge using [Claude Managed Agents](https://platform.claude.com/docs/en/managed-agents/overview) (CMA). CMA provides [the agent harness as well as a hosted sandbox](https://www.anthropic.com/engineering/managed-agents), so it’s well-suited for long-running tasks with Fable 5. For Parameter Golf, I gave CMA access to 8xH100 GPUs as a [self-hosted sandbox](https://platform.claude.com/docs/en/managed-agents/self-hosted-sandboxes).

One subtle point: what does the judging is important. We’ve seen that models have problems with self-critique on their own outputs. Prithvi Rajasekaran wrote about this in our engineering blog [here](https://www.anthropic.com/engineering/harness-design-long-running-apps).

![图像](https://pbs.twimg.com/media/HKYo5xEaMAAjeKL?format=jpg&name=large)

We’ve found that a verifier sub-agent [tends to outperform](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-fable-5) self-critique with Fable 5, because grading is done in an independent context window. [Outcomes](https://platform.claude.com/docs/en/managed-agents/define-outcomes) in CMA handles this by spawning a grader sub-agent for you.

For each test, I supplied a rubric (a file) with the nine checkable criteria (e.g., run a baseline, run 20 experiments, etc). Then, I ran Parameter Golf for up to 8 hours. The Outcomes grader confirmed that all experimental criteria were met before allowing Claude to stop the work.

Fable 5 improved the training pipeline ~6x more than Opus 4.7. If we consider experiments as structural (e.g., architecture changes) or scalar (e.g., adjusts a constant), Fable 5 bet on larger structural changes and showed resilience (e.g., pushing through a quantization regression to its biggest win).

Opus 4.7's first experiment produced a small win and nearly everything after followed the same template: adjust a scalar, measure, keep if positive.

**Memory**

Memory is [another area where Fable excels](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices). We can think about this as a outer loop that spans across sessions: Claude writes to memory during a session and those memories can be retrieved in future sessions.

[@pgasawa](https://x.com/@pgasawa) and team recently published Continual Learning Bench 1.0, so I wanted to test this on Fable 5 vs earlier models.

> 5月5日
> 
> 今天，我们发布了 Continual Learning Bench 1.0：第一个用于衡量 AI 系统在在线环境中如何改进的现实基准。 如今的基准假设模型是无状态的。每个示例都是独立的，一旦系统完成一项任务，它就会继续进行，仿佛什么都没发生过。 但部署的 AI 系统应该从经验中学习。我们将 10+

I compared Fable 5, Opus 4.7, and Sonnet 4.6 on one of the tasks from the benchmark: the task asks an agent to answer sequential questions given access to a SQL database. Each question is a separate agent session and memory is provided.

For this, I used CMA with [memory](https://platform.claude.com/docs/en/managed-agents/memory), which gives each agent access to a mounted filesystem that can be shared across sessions.

![图像](https://pbs.twimg.com/media/HKYq6HvaMAEfFJg?format=jpg&name=large)

For this task, effective use of memory benefits from a progression: fail (get something wrong and document), investigate (before moving on, figure out why), verify (turn the diagnosis into a checked fact), distill (turn verification into a general rule), and consult (read the rule, instead of re-deriving it).

Sonnet 4.6 exits around step 1: its store is a list of failure notes and open guesses (e.g., "maybe prc instead of prc\_usd?"). It rarely consults prior notes. To improve performance, task-specific memory instructions are needed.

Opus 4.7 exits around step 3: it creates a schema reference with uncertainty flagged (e.g., "possibly prc in cents? Verify."), but verification coverage is low: at 7-33% of questions (median run ~17%).

Fable 5 tends to complete the progression: in its strongest runs, verification coverage is up to 73% (22 of 30) and it distills learnings into general rules that help with future tasks.

Rather than directly prompting and steering Fable 5, it's often better to design loops that let the model to self-correct in response to environment feedback (e.g., /goal or Outcomes) and manage its own context (e.g., via memory).

I've shared just a few small scale experiments that I've run, but its worth testing Fable 5 for yourself on challenging tasks and using loops for self-correction or memory.

To get started, see our [docs](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-fable-5) or ask the latest version of Claude Code, which can use our built-in [/claude-api](https://github.com/anthropics/skills/tree/main/skills/claude-api) skill to tell you about Fable 5 (e.g., prompting best practices), /goal, Claude Managed Agents, or other API features.