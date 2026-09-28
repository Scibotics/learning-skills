# learning-skills

> Built on top of [Amos Blomqvist](https://github.com/amosblomqvist)'s AI learning system. All credit for the original idea and teaching philosophy goes to him. Watch his video: [How I Use AI to Learn Things](https://www.youtube.com/watch?v=kzcI5F4tGiU).
>
> **My modifications:** I tailored the system specifically to computer science. After a lesson, it creates a software exercise for you to build (`exercise`), and it gives you recall questions to prepare for upcoming interviews (`recall`).

## TL;DR

A [pi](https://github.com/earendil-works/pi) configuration that turns your agent into a tutor. Instead of pasting a wall of text at you, it finds out what you already know, builds the idea up from first principles, and checks your understanding as you go. Then it helps you practice what you learned and tests whether it stuck.

**Example: learning why transformers use attention**

Your agent teaches you step by step by understanding your baseline and building a lesson from there.
```
You:    /teach Teach me self-attention.

Agent:  Before we start: what does an RNN do with the word "it" in
        "The cat sat because it was tired"?
        (quiz) a) looks back at every word directly
               b) relies on a hidden state carried word by word
               c) ignores context

You:    b

Agent:  ✓ Right. So by the time the RNN reaches "it", "cat" only survives
        as whatever got squeezed into that hidden state. How could you let
        "it" look at "cat" directly? ...
        → builds up query/key/value as the answer to that question
        → quizzes you on why we scale by √d_k
        ...
```

Create implementation exercises based on your lessons
```
You:    /exercise notes/self-attention.md projects/mini-attention
        → a 1–5 hour project where you implement attention yourself
```

Recall what you've learned for interview prep
```
You:  /recall interview me on transformers
        → mock-interview questions graded against your own lesson notes
```

## Requirements

- [pi](https://github.com/earendil-works/pi)
- **A subagent implementation**, so the system can spawn the researcher (fact-checking) and the diagram makers. Recommended: [pi-interactive-subagents](https://github.com/amosblomqvist/pi-interactive-subagents) (tmux only), which works out of the box. Other implementations work too, but you may need to adapt `agents/*.md`. For example, `agents/researcher.md` uses `safe_bash`, which is specific to that extension.
- **The bundled `ask-user-question` extension.** If your setup already has one, use this copy instead. Popups from different extensions share a UI lock, and that lock only works when they come from the same implementation.
- **Optional:** a Markdown viewer that renders LaTeX (e.g. [Obsidian](https://obsidian.md)) for reading `/md-log` files.
- **Optional, for `exercise`:** the `claude`, `codex`, or `agy` CLI if you want an external agent to write the starter code.

## Setup

This repo **is** a `.pi` directory. From your learning project's root (e.g. your notes vault):

```bash
git clone https://github.com/Scibotics/learning-skills .pi
```

Then open pi in the project root. You can also copy just the pieces you want into an existing `.pi` config.

The system still works without subagents, since the main session does the teaching. You only lose the researcher and the generated visuals. The `teach` skill was written for one learner, so edit it to match how you learn best.

## Skills

### `teach`

The core of the system. Whenever the agent explains something, it follows two principles:

1. **Unconditional truths first.** Every new fact is built from foundations you already accept, so it becomes part of a connected mental model instead of something you memorized.
2. **"How could I have discovered this?"** Ideas are presented as the natural answer to a problem, so you could have come up with them yourself.

Each lesson runs as **probe → plan → teach**. The agent first quizzes you to find what you already know, plans a path from there to the target idea, and then teaches in short steps with a quiz after each one.

### `exercise`

Turns a completed lesson into a focused, portfolio-quality coding project (1–5 hours).

```
/exercise <lesson.md> <project-directory>
```

It reads the lesson, proposes an exercise, and waits for your approval. It then writes an `EXERCISE.md` handoff and generates starter code. You pick who writes the scaffold: `claude`, `codex`, `agy`, or the agent itself. The core idea is always left for you as `TODO(learner)` stubs, and the agent only builds the surrounding plumbing.

### `recall`

A technical mock interview that checks what you actually remember. Give it a **topic** ("DQN", "clustering") or paste a **job description**, and tell it how many questions you want. It searches your lesson notes and asks open-ended interview questions one at a time. Each answer is graded ✅ Strong / 🟡 Partial / ❌ Missed, with feedback that points to the lesson to review. It finishes with a scorecard of what you know well, what to revisit, and any topics your lessons don't cover yet.

Every question comes from a lesson you've done. This skill assesses you and doesn't teach.

## Logging a session to Markdown

The terminal is a poor place to read long lessons with math and code. `/md-log` mirrors the session into a Markdown file so you can read it rendered, e.g. in Obsidian.

```
/md-log notes/self-attention.md   # link an existing .md file and backfill the session so far
/md-unlog                         # stop logging
```

- The file must already exist. `/md-log` never creates files.
- It records your prompts, the lesson text, and quiz Q&A. Tool noise like bash, reads, and edits is left out.
- Quiz questions appear in the log before you answer, and the answer is added afterward, so reading along never spoils the answer.
- The resulting file is the lesson note that `exercise` and `recall` build on.
