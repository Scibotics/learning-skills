---
name: recall
description: Run a technical mock interview that tests the user's recall of the lessons in this learning vault (AI, ML, RL, imitation learning, CS, software development). Searches the lesson notes, writes open-ended interview-style questions grounded in them, asks them one at a time, and grades each answer against the lesson. Use this whenever the user wants to be quizzed, grilled, interviewed, or tested on what they've learned; pastes a job description and wants to practice for it; says "interview me on X", "test my recall", "what do I remember about DQN", "prep me for this role", or wants to check whether a lesson stuck, even if they don't say "mock interview".
---

# Recall

The point of this skill is **assessment, not teaching**. The user has already worked through lessons in this vault. Now they want to find out what actually stuck, under interview conditions: you ask, they answer from memory, you grade honestly against what the lesson established. Teaching mid-question defeats the purpose. Save explanations for the feedback after each answer.

Every question must trace back to a lesson the user has actually done. A question about material they never studied tells them nothing about their recall. It's just a new-material quiz. Topics that the request mentions but no lesson covers get reported as gaps instead.

## Step 1: Pin down the request

The user supplies either a **topic** (e.g. "DQN", "value-based RL", "clustering") or a **job description** (pasted text, often long, listing skills and responsibilities).

Also figure out **how many questions** they want. If they didn't say, ask before generating anything. Use an ask-question tool if one is available (offer something like 3 / 5 / 8 / 12), otherwise ask in plain text. Don't guess a number. The user specifically wants to be asked, since the session length depends on it.

If they gave no topic or JD at all, ask whether they want the whole vault or a particular area. Show the list of available lessons from Step 2 so they can pick.

## Step 2: Inventory the lessons

Lessons are the markdown files in the vault root and its subfolders. The vault root is the directory containing `.pi/` and `.obsidian/`, normally the current working directory. Ignore dot-directories (`.pi`, `.obsidian`, `.claude`, `.git`) and empty files:

```bash
find . -name '*.md' -not -path '*/.*' -size +0 | sort
```

Folder names describe lessons well (e.g. `Reinforcement Learning/0002 - DQN/`). A file's own opening request (the line after `> [!note] SKILL loaded: ...`) says what the user set out to learn. Grep that line when a filename such as `Lesson.md` is generic:

```bash
grep -m1 -A3 'SKILL loaded' <file> | tail -2
```

## Step 3: Map the request to lessons

- **Topic:** pick the lessons that cover it, including closely related prerequisites (a DQN interview can reasonably reach back to tabular Q-learning). Use `grep -ril` on key terms to catch lessons that cover the topic in passing.
- **Job description:** pull out the concrete technical skills and concepts it asks for. Ignore soft skills, years of experience, and tooling you can't probe from lessons. For each one, decide whether a lesson covers it. Keep two lists: **covered** (skill → lesson files) and **gaps** (skills with no lesson). Weight questions toward the JD's emphasis, within what's covered.

If nothing in the vault matches, say so plainly, name the closest lessons that do exist, and ask how to proceed. Don't invent questions from general knowledge.

Tell the user briefly which lessons you'll draw from, and for a JD, which requirements are gaps. Then start. Keep it to a couple of lines, since the questions are the main event.

## Step 4: Read the lessons and build a private question plan

Read each selected lesson in full. They are transcripts of teaching sessions, so the substance is spread across explanations and quizzes. Callouts to know:

- `[!quote] YOU` marks the user's own messages. `[!abstract] PI` marks tutor explanations. These hold most of the content.
- `[!question] Quiz` followed by `[!success]` or `[!failure] Quiz — incorrect ✗`. **Failures and `Quiz — I don't know` entries are known weak spots.** Probe them, since a real test of recall revisits what was shaky.
- Headings such as `## Dependency map` often list the core truths the lesson was built on. These make good anchors for "explain from first principles" questions.

For each question, privately note (don't show the user):
- the question text
- the source lesson file
- 2–4 **key points** a strong answer must hit, taken from what the lesson actually says
- difficulty (foundation / core / deep)

Aim for a spread of question types, the way a real technical interviewer probes:
- **Explain / define**: "What problem does experience replay solve?"
- **Why / derive**: "Why does taking a max over noisy Q-estimates bias the value upward?"
- **Compare / tradeoff**: "DQN vs Double DQN: what exactly changes in the target, and why does that help?"
- **Failure mode / what-if**: "What happens if you update the target network every step?"
- **Apply**: "You're training an agent on a sparse-reward task and it never learns. How might curriculum learning help, and how would you design one?"

Order the questions from foundation to deep. Spread them across the selected lessons instead of clustering on one. Rephrase rather than reusing a lesson's multiple-choice quiz verbatim. The user has seen those options, so recognition would stand in for recall. Keep questions open-ended and free-response, with no answer options.

## Step 5: Run the interview, one question at a time

Show a progress marker, then the question only, e.g. `**Q2/5** · Double DQN`. Then stop and wait for the answer. Don't hint, don't list what a good answer includes, and don't ask the next question in the same message.

When they answer:

1. **Grade against the key points**:
   - ✅ **Strong**: hits the key points with correct reasoning
   - 🟡 **Partial**: right direction, but a key point is missing, vague, or slightly wrong
   - ❌ **Missed**: wrong, or the core idea is absent

   Judge substance, not vocabulary. A correct explanation in the user's own words counts fully.
2. **Give short feedback**: what they got right, what was missing or wrong, and the model answer in a few sentences. Cite the lesson (e.g. `Reinforcement Learning/0004 - Double DQN/Learning Double DQN.md`) so they know where to review.
3. **Optionally ask one follow-up**, like an interviewer would, when a Partial answer is one step from Strong or a Strong answer invites a natural "and why?". The follow-up counts as part of the same question, not toward the total.
4. Move to the next question.

If they say "I don't know" or "skip", mark it ❌, give the model answer, and move on without judgment. That's useful data, not failure. If they want to stop early, jump to the summary.

## Step 6: Summary

After the last question, give a scorecard:

```
## Mock interview results: <topic or role>

| # | Topic | Result |
|---|-------|--------|
| 1 | ... | ✅ |

Score: X strong · Y partial · Z missed

**Solid:** <concepts they clearly own>
**Review:** <concepts to revisit, each with its lesson file>
**Gaps (not in any lesson yet):** <JD requirements with no lesson; omit for topic interviews if none>
```

End with one concrete next step, such as re-running the `teach` skill on the weakest concept or starting a lesson on the biggest gap.
