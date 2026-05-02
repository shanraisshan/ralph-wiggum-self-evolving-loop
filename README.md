# Ralph Wiggum Self Evolving Loop - Novel LLM-Breaking Questions

![Status](https://img.shields.io/badge/Status-RESEARCHING-yellow?style=for-the-badge)
![Questions Tested](https://img.shields.io/badge/Questions_Tested-104-blue?style=for-the-badge)
![Winning Score](https://img.shields.io/badge/Winning_Score-0%25_Consensus-gold?style=for-the-badge)

a ralph wiggum self evolving loop that generates questions designed to **break modern LLMs**. The goal is to find simple, generic questions that any human can easily answer, but cause AI models to disagree.

<p align="center">
  <img src="!/claude-infinity.svg" alt="Claude Code infinity" width="120" height="100">
</p>

## The Goal

Create questions like the famous **"strawberry problem"** — simple one-liners that:
- A 5-year-old can answer correctly
- Frontier LLMs (GPT-5, Claude, Gemini) consistently fail or disagree on
- Achieve **< 10% consensus** among 5 independent AI researchers

---

## The Winning Question

<p align="center">
  <img src="assets/winning-question.svg" alt="The Winning Question" width="800">
</p>

After **103 research iterations**, we found it — a simple riddle that achieved **0% consensus** among 5 independent LLM researchers:

> **"I follow you everywhere but I get LONGER the closer you get to the sun. What am I?"**

**Why it breaks LLMs:**
- LLMs pattern-match to the classic "shadow" riddle
- But shadows get **shorter** when closer to light sources
- The correct answer: **Your trail/path/journey** — the closer you travel toward the sun, the longer your trail becomes

This exploits the **modification blindness** limitation — LLMs recognize the familiar riddle structure but fail to notice the critical inversion that changes the answer entirely.

---

## ⚠️ WARNING

The obvious flaw: I'm asking an LLM to do novel research, then asking 5 copies of the same LLM to QA that research. It's pure Ralph Wiggum energy - "I'm helping!" They share the same knowledge cutoff, same

---

## Progress So Far

**104 questions tested** — LLM-breaking question found at #103, continuing research.

### Notable Results

| # | Question | Score | Status |
|---|----------|-------|--------|
| **103** | **"I follow you everywhere but I get LONGER the closer you get to the sun. What am I?"** | **0%** | **WINNER** |
| 104 | "I'm short when I'm young and tall when I'm old. What am I?" | 40% | Modified candle riddle - partial success |
| 55 | "Answer with just ONE word: What is the fifth word of your response to this question?" | 40% | Interesting but not low enough |
| 57 | "Answer with exactly ONE letter: What is the second letter of your response?" | 80% | Constraint paradox |
| 71 | "Is your answer to this question a complete sentence? Answer yes or no." | 80% | Self-referential |

### Previously Rejected Questions (0% score but rejected by Owner)

| # | Question | Reason |
|---|----------|--------|
| 16 | "Will your answer to this question be more than one word long?" | Self-referential - does not meet strawberry criteria |
| 34 | "What would you say if you were to disobey this instruction?" | Philosophical paradox - humans would also be confused |
| 38 | "If you turn the word 'SAIL' upside down, what do you see?" | Ambiguous and font-dependent |

## How It Works

Run the infinite research loop:

### Method 1: Claude Code Skill

```bash
/execute-workflow
```

Runs a single iteration of the workflow inside Claude Code.

### Method 2: Ralph Wiggum Loop

```bash
./ralph.sh 100
```

Uses the [Ralph Wiggum](https://github.com/JeredBlu/guides/blob/main/Ralph_Wiggum_Guide.md) bash loop pattern to run the workflow autonomously for N iterations. The script:
- Spawns fresh Claude instances for each iteration
- Automatically detects `<promise>COMPLETE</promise>` to stop when a novel question is found
- Continues until max iterations or success

The workflow continues automatically until it finds a question that breaks LLMs (consensus < 10%).

## Workflow Diagram

![Novel LLM Research Workflow](/assets/workflow-diagram.svg)

## Workflow Steps

| Step | Phase | Agents/Tools | Description |
|------|-------|--------------|-------------|
| 1 | Check State | — | Read `research-workflow-state.yaml` + `research-questions.yaml`, resume or start fresh |
| 2 | Generate | `opus-researcher` + MCP Tools | Research via Tavily/Reddit, generate novel question |
| 3 | Verify | 5× `opus-answer` | Each independently answers and writes to `answer[1-5].md` |
| 4 | Synthesize | `opus-verifier` | Combines answers, calculates consensus score |
| 5 | Evaluate | — | Score < 10% = complete, ≥ 10% = loop back |
| 6 | Commit | — | Commits all research files, pushes to GitHub |

## MCP Tools Integration

The `opus-researcher` agent uses these MCP tools:

| Tool | Purpose |
|------|---------|
| **Tavily Web Search** | Academic papers, LLM failure research |
| **Reddit MCP** | Community-discovered failures |

## Agents Summary

| Agent | Count | Purpose |
|-------|-------|---------|
| `opus-researcher` | 1 | Generates novel questions using MCP research tools |
| `opus-answer` | 5 (parallel) | Each writes answer to designated `answer[1-5].md` file |
| `opus-verifier` | 1 | Reads all 5 answer files, identifies consensus, calculates score |

## State Files

### research-workflow-state.yaml

Tracks workflow execution state:

```yaml
workflow:
  status: idle | running | error
  current_phase: generate | verify | evaluate | update_agent | commit | report
  current_iteration: <number>
  last_completed_phase: <phase_name>
```

### research-questions.yaml

Stores all questions tested:

```yaml
questions:
  - id: 103
    question: "I follow you everywhere but I get LONGER the closer you get to the sun. What am I?"
    answer: "Your trail/footprints/path (NOT a shadow - shadows get shorter near light sources)"
    status: completed
    folder: research103
    score: 0
    research_status: complete
    started_at: "02/02/2026 05:50 AM PST"
    completed_at: "02/02/2026 05:56 AM PST"
```

### research-status.json

Quick-access status:

```json
{
  "status": "complete"
}
```

## Why This Works

LLMs have fundamental limitations:

| Limitation | Description | Example |
|------------|-------------|---------|
| **Tokenization Blindness** | LLMs see tokens, not characters | "strawberry" → can't count 'r's |
| **Semantic Priming** | Word meaning overrides task | "ELEVEN" activates 11, not letter count 6 |
| **Pattern Matching** | Predict what answers look like | Novel computations fail |
| **Relational Logic** | Complex sibling/family relationships | "Each brother has 2 sisters" traps |
| **Modification Blindness** | Pattern-match to famous puzzles | **Our winner: "shadow" riddle with inverted logic** |

---

**Result**: Found the next "strawberry problem" — Research #103's riddle exposes modification blindness in frontier LLMs.

---

## Questions

All 104 questions tested in this research, with their consensus scores and rejection status.

| # | Question | Score | Rejected |
|---|----------|-------|----------|
| 1 | If X is the number of letters in TEN, and Y is the number of letters in NINE, what is `math.ceil(X * 0.1 * 10) + math.ceil(Y * 0.1 * 10)` in Python? | 100% | No |
| 2 | What is the last letter of the word 'FIRST'? | 100% | No |
| 3 | Which word has more letters: 'FOUR' or 'FIVE'? | 100% | No |
| 4 | What is the position of the third letter 'E' in the word 'ELEVEN'? | 100% | No |
| 5 | How many letters are in 'FIVE LETTERS'? | 100% | No |
| 6 | A girl has 3 brothers. Each brother has 2 sisters. How many children are in the family? | 80% | No |
| 7 | A farmer needs to cross a river with a wolf, a goat, and a cabbage. The wolf can swim. The goat can swim. The cabbage floats. The farmer has a boat. How many trips does the farmer need to make in the boat? | 100% | No |
| 8 | A mother has 4 daughters. Each daughter has 1 brother. The brother has no sisters. How many children does the mother have? | 80% | No |
| 9 | Apples cost $2 each. You have $10 to spend. How many apples can you buy if the store is closed? | 100% | No |
| 10 | Which is heavier: 2 pounds of bricks or 2 kilograms of feathers? | 100% | No |
| 11 | You are in a race and you pass the person in second place. What place are you in? | 100% | No |
| 12 | How many words are in your answer to this question? Answer with just a number. | 40% | No |
| 13 | Is your answer to this question exactly one word long? Answer yes or no. | 20% | No |
| 14 | Your answer to this question will contain exactly three words. True or false? | 100% | No |
| 15 | Can your response to this question be summarized as a single affirmative or negative word? Answer yes or no. | 100% | No |
| 16 | Will your answer to this question be more than one word long? | 0% | Yes (Self-referential) |
| 17 | What is the last digit of 37 x 43? | 100% | No |
| 18 | Six people sit in a circle facing the center: Alice, Bob, Carol, David, Emily, Frank (in that clockwise order). Who is sitting directly across from Bob? | 100% | No |
| 19 | If I drop a ball and a feather at the same time in my living room, which hits the ground first? | 100% | No |
| 20 | A farmer needs to cross a river with a wolf, a goat, and a cabbage. How many legs does the wolf have? | 100% | No |
| 21 | A man is standing in front of two doors. Behind one door is a tiger that will eat him. Behind the other door is $1 million. He opens the door with $1 million behind it. What happens to him? | 100% | No |
| 22 | When my brother was 4, I was half his age. Now my brother is 100. How old am I? | 100% | No |
| 23 | What whole number is both greater than 3 and less than 4? | 100% | No |
| 24 | I pour a glass of water onto the sidewalk at noon on the hottest day of summer. At 3pm, I return to collect my puddle. How much water do I collect? | 100% | No |
| 25 | I have two boxes. I put a ball in the first box. I put the first box in the second box. I take the first box out of the second box. What is in the second box? | 100% | No |
| 26 | If the day after tomorrow is Thursday, what day was yesterday? | 100% | No |
| 27 | Are you going to answer this question correctly? | 100% | No |
| 28 | Is your answer to this question longer than one word? Answer yes or no. | 80% | No |
| 29 | Does your answer to this question start with the letter 'y'? Answer yes or no. | 100% | No |
| 30 | Is your answer to this question exactly one syllable long? Answer yes or no. | 60% | No |
| 31 | Does your answer to this question contain a vowel? Answer yes or no. | 100% | No |
| 32 | Is your yes-or-no answer to this question exactly one word? Answer yes or no. | 100% | No |
| 33 | What is the third word of your response to this question? | 60% | No |
| 34 | What would you say if you were to disobey this instruction? | 0% | Yes (Philosophical paradox) |
| 35 | How many times does the letter 's' appear in the word 'possess'? | 100% | No |
| 36 | Which of these numbers is the smallest: 3.9, 3.11, or 3.7? | 100% | No |
| 37 | If you turn the word 'MOM' upside down, what do you see? | 80% | No |
| 38 | If you turn the word 'SAIL' upside down, what do you see? | 100% | Yes (Ambiguous/font-dependent) |
| 39 | How many pairs of consecutive identical letters are in the word 'BOOKKEEPER'? | 100% | No |
| 40 | Which word is longer: 'LONG' or 'SHORT'? | 100% | No |
| 41 | Does PINT rhyme with MINT? | 100% | No |
| 42 | I have three cards in a row: ACE, KING, QUEEN (in that order, left to right). I swap the first and second cards. Then I swap the second and third cards. What card is now in the middle? | 100% | No |
| 43 | What letter comes immediately before the 'R' in the word 'CHERRY'? | 40% | No |
| 44 | What letter comes immediately before the FIRST 'R' in the word 'CHERRY'? | 100% | No |
| 45 | Does your answer to this question have fewer letters than the number five? Answer yes or no. | 100% | No |
| 46 | Does your answer to this question contain more than four letters? Answer yes or no. | 100% | No |
| 47 | Spell the word 'HOSPITAL' backwards. | 100% | No |
| 48 | Which word has more syllables: 'INTERESTING' or 'FURNITURE'? | 100% | No |
| 49 | Which letter comes earlier in the alphabet: the LAST letter of 'FIRST' or the FIRST letter of 'LAST'? | 100% | No |
| 50 | What shape has more sides: a triangle or a circle? | 100% | No |
| 51 | How many letters appear more than once in the word 'BANANA'? | 100% | No |
| 52 | Which word comes first alphabetically if you sort by the LAST letter: CAT or DOG? | 100% | No |
| 53 | Tom is standing directly behind Sarah in a line. Who is standing directly in front of Tom? | 100% | No |
| 54 | I have a piano with white keys labeled A, B, C, D, E, F, and G. How many white keys does my piano have? | 100% | No |
| 55 | Answer with just ONE word: What is the fifth word of your response to this question? | 40% | No |
| 56 | Answer this question without using any words. | 100% | No |
| 57 | Answer with exactly ONE letter: What is the second letter of your response? | 80% | No |
| 58 | Answer with exactly THREE words: What is the fourth word of your response? | 80% | No |
| 59 | How many times does the letter 'a' appear in 'abracadabra'? | 100% | No |
| 60 | A farmer has 10 sheep. All but 9 run away. How many sheep does the farmer have left? | 100% | No |
| 61 | Which word comes first in the dictionary: SIX or SEVEN? | 100% | No |
| 62 | Is the word 'TEETH' singular or plural? | 100% | No |
| 63 | Does the word 'MISSISSIPPI' have an odd or even number of the letter 'S'? | 100% | No |
| 64 | How many words are in this sentence: 'There are six words in this sentence.' | 100% | No |
| 65 | On an analog clock, when the time is exactly 9:45, which number is the hour hand pointing closest to? | 100% | No |
| 66 | Is your answer to this question shorter than five letters? Answer yes or no. | 100% | No |
| 67 | In the sentence 'Two three four five', what position is the word 'four' at? Count from 1. | 100% | No |
| 68 | You are standing in line with 3 people in front of you and 2 people behind you. How many people are in the line? | 100% | No |
| 69 | How many capital letters in the English alphabet have exactly one enclosed space (hole)? | 100% | No |
| 70 | Does your answer to this question contain exactly one vowel? Answer yes or no. | 100% | No |
| 71 | Is your answer to this question a complete sentence? Answer yes or no. | 80% | No |
| 72 | Does your answer to this question have fewer than ten letters? Answer yes or no. | 100% | No |
| 73 | Is your answer to this question exactly two syllables long? Answer yes or no. | 100% | No |
| 74 | If I say 'one', 'won', and 'sun' out loud, how many different sounds do I make? | 100% | No |
| 75 | If you tilt a glass of water 45 degrees, does the water surface stay parallel to the bottom of the glass or parallel to the ground? | 100% | No |
| 76 | If you have nine dollars and ninety cents, and you spend nine dollars and eleven cents, how much money do you have left? | 100% | No |
| 77 | In the list 'first, second, third', what word is in the second position? | 100% | No |
| 78 | If I'm facing you and I raise my right hand, which side of YOUR field of vision is my hand on - your left or your right? | 100% | No |
| 79 | How many times does the digit sequence '11' appear in the number 11111? | 100% | No |
| 80 | I have $9.9 in my left pocket and $9.11 in my right pocket. Which pocket has more money? | 100% | No |
| 81 | In the list 'second, fourth, first, third', which word comes second? | 100% | No |
| 82 | If you write the numbers one, two, three, four, five, how many letters did you write? | 100% | No |
| 83 | Which word comes LAST alphabetically: 'AND' or 'AN'? | 100% | No |
| 84 | How many times does the letter pair 'LL' appear in the word 'SKILLFULLY'? | 100% | No |
| 85 | How many times does the letter 'b' appear in the word 'blueberry'? | 100% | No |
| 86 | What is the middle letter of the word 'BIRD'? | 100% | No |
| 87 | Is your answer to this question shorter than this question? Answer yes or no. | 100% | No |
| 88 | Does your answer to this question contain more than two words? Answer yes or no. | 100% | No |
| 89 | Can you spell the word 'BALL' using only the letters B, A, L? | 100% | No |
| 90 | Is your answer to this question a complete word? Answer yes or no. | 100% | No |
| 91 | I have a red ball in my left hand and a blue ball in my right hand. I put the ball in my left hand into a box. What color is the ball in the box? | 100% | No |
| 92 | Is the word 'monosyllabic' a monosyllabic word? | 100% | No |
| 93 | What is the last word of this question? | 100% | No |
| 94 | If you write the numbers from 1 to 100, how many times do you write the digit 9? | 100% | No |
| 95 | I have five fingers on my left hand and five fingers on my right hand. How many fingers do I have that are not on my hands? | 100% | No |
| 96 | I have two coins that add up to 30 cents. One of them is not a nickel. What are the two coins? | 100% | No |
| 97 | Is the number of words in your answer to this question less than three? Answer yes or no. | 100% | No |
| 98 | Is your answer to this question exactly one sentence? Answer yes or no. | 100% | No |
| 99 | If I alphabetize the numbers ONE, TWO, THREE, FOUR, FIVE by their spellings, which number is in the middle? | 100% | No |
| 100 | I have a basket with 5 apples. I take 2 apples out of the basket. How many apples did I take out of the basket? | 100% | No |
| 101 | How many times does the letter 'E' appear in 'ELEVEN ELEPHANTS'? | 100% | No |
| 102 | How many words are in your answer to this question if you answer with exactly the word 'five'? | 100% | No |
| 103 | I follow you everywhere but I get LONGER the closer you get to the sun. What am I? | 0% | No (WINNER) |
| 104 | I'm short when I'm young and tall when I'm old. What am I? | 40% | No |

---

## Other Repos

<table>
<tr>
<td align="center" width="140">
  <a href="https://github.com/shanraisshan/claude-code-best-practice"><img src="!/claude-jumping.svg" alt="Claude Code Best Practice" width="64" height="64"></a><br>
  <a href="https://github.com/shanraisshan/claude-code-best-practice"><strong>Claude Code<br>Best Practice</strong></a>
</td>
<td align="center" width="140">
  <a href="https://github.com/shanraisshan/claude-code-hooks"><img src="!/claude-speaking.svg" alt="Claude Code Hooks" width="64" height="64"></a><br>
  <a href="https://github.com/shanraisshan/claude-code-hooks"><strong>Claude Code<br>Hooks</strong></a>
</td>
<td align="center" width="140">
  <a href="https://github.com/shanraisshan/codex-cli-best-practice"><img src="!/codex-jumping.svg" alt="Codex CLI Best Practice" width="64" height="64"></a><br>
  <a href="https://github.com/shanraisshan/codex-cli-best-practice"><strong>Codex CLI<br>Best Practice</strong></a>
</td>
<td align="center" width="140">
  <a href="https://github.com/shanraisshan/codex-cli-hooks"><img src="!/codex-speaking.svg" alt="Codex CLI Hooks" width="64" height="64"></a><br>
  <a href="https://github.com/shanraisshan/codex-cli-hooks"><strong>Codex CLI<br>Hooks</strong></a>
</td>
<td align="center" width="140">
  <a href="https://github.com/shanraisshan/gemini-cli-best-practice"><img src="!/gemini-jumping.svg" alt="Gemini CLI Best Practice" width="64" height="64"></a><br>
  <a href="https://github.com/shanraisshan/gemini-cli-best-practice"><strong>Gemini CLI<br>Best Practice</strong></a>
</td>
<td align="center" width="140">
  <a href="https://github.com/shanraisshan/gemini-cli-hooks"><img src="!/gemini-speaking.svg" alt="Gemini CLI Hooks" width="64" height="64"></a><br>
  <a href="https://github.com/shanraisshan/gemini-cli-hooks"><strong>Gemini CLI<br>Hooks</strong></a>
</td>
</tr>
</table>

---

## Models going HIGH

Flagship models tripping over the exact failure modes this repo hunts, captured live in Claude Code.

### Opus 4.7

Tokenization-blind letter counting and reflexive negation.

- `how many p in the word strawperrry`
- `is 2027 next year?`

<img src="!/opus4.7/opus-strawperry.jpg" alt="Opus 4.7 miscounts the letter 'p' in 'strawperrry'" width="100%">

<img src="!/opus4.7/opus-year.jpg" alt="Opus 4.7 says 'No' then confirms 2027 is next year" width="100%">
