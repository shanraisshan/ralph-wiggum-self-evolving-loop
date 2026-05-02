# Implementation Guide: Self-Evolving Agentic Loop

This document maps the novel-llm-26 architecture to the nine foundational principles of self-evolving agentic loops. If you are studying these patterns through the [Disrupt Labs training material](https://hub.dsrpt.dev/courses/self-evolving-agentic-loops/), this repo serves as a complete, runnable reference implementation.

---

## How this repo implements self-evolving loop principles

### 1. The Ralph Wiggum pattern (inner loop engine)

The `ralph.sh` script is a textbook Ralph Wiggum implementation. It spawns a fresh Claude Code instance per iteration, passes a fixed prompt (`prompt.md`), and uses binary backpressure to decide whether to continue or stop.

```bash
./ralph.sh 100    # Up to 100 iterations
```

Each iteration:
- Receives the task via `/execute-workflow`
- Runs autonomously with no human intervention
- Outputs `<promise>COMPLETE</promise>` when consensus score < 10% (binary backpressure)
- Otherwise, the loop spawns the next iteration

This maps directly to **Layer 1** of the composable architecture -- a tight inner loop with zero overhead between iterations.

### 2. State machine workflow (resumable execution)

The workflow is a six-phase state machine that can be interrupted and resumed at any phase:

```
idle -> generate -> verify -> evaluate -> update_agent -> commit -> report
```

State is tracked in `research/research-workflow-state.yaml`:

```yaml
workflow:
  status: idle | running | error
  current_phase: generate | verify | evaluate | update_agent | commit | report
  current_iteration: 103
  last_completed_phase: commit
```

If the process crashes mid-verify, the next invocation reads the state file and resumes from the verify phase -- no work is lost, no phases are repeated.

### 3. Accumulating intelligence (self-evolving researcher)

This is where the system goes beyond a basic Ralph Wiggum loop. The `opus-researcher` agent (`.claude/agents/opus-researcher.md`) grows with every iteration:

- After each failed attempt (score >= 10%), the **update_agent** phase appends findings to the researcher's accumulated knowledge section
- The agent's "Evolved Strategy" section is updated with lessons learned
- By iteration 104, the agent file grew to 1,549 lines of accumulated research findings

This implements the **memory spectrum** from implicit to explicit:
- **Implicit memory**: Each iteration reads `previous-research-summary.md` from the prior iteration (short-term context)
- **Explicit memory**: The agent file itself accumulates findings across all iterations (long-term knowledge)
- **Cross-session persistence**: Because the agent file is committed to git, knowledge survives across fresh Claude instances

### 4. Multi-agent verification (parallel consensus)

The verify phase demonstrates multi-agent coordination:

1. The question is extracted from `research-questions.yaml`
2. Five parallel `opus-answer` agents independently answer the question
3. Each agent writes to its own file (`answer1.md` through `answer5.md`)
4. The `opus-verifier` agent reads all five answers and calculates a consensus score

```
                    +--> opus-answer-1 --> answer1.md --+
                    |                                    |
question.yaml ----->+--> opus-answer-2 --> answer2.md --+--> opus-verifier --> score
                    |                                    |
                    +--> opus-answer-3 --> answer3.md --+
                    |                                    |
                    +--> opus-answer-4 --> answer4.md --+
                    |                                    |
                    +--> opus-answer-5 --> answer5.md --+
```

The answer agents are deliberately simple (18 lines) -- they respond naturally like a standard chatbot. This isolation ensures each answer is independent, not influenced by other agents' reasoning.

### 5. Binary backpressure and evaluation-driven development

The system uses pure binary backpressure as its evaluation signal:

| Consensus score | Signal | Action |
|----------------|--------|--------|
| < 10% | `complete` | Stop the loop -- the question breaks LLMs |
| >= 10% | `need_more_research` | Continue iterating with updated knowledge |

This is Evaluation-Driven Development (EDD) in its simplest form: the evaluator (consensus score) drives the evolution (researcher agent updates). Without this backpressure, the loop would be an expensive random walk.

The verifier acts as an **LLM-as-judge** -- it reads all five answers, identifies agreement and disagreement, and produces a structured score. This is the evaluator-optimizer pattern where the verifier's judgment drives the researcher's next iteration.

### 6. Dead-end detection through knowledge accumulation

The system does not have explicit dead-end detection, but the accumulating researcher agent achieves a similar effect:

- Failed categories of questions (e.g., self-referential paradoxes, pure math) are recorded in accumulated findings
- The evolved strategy section steers future iterations away from exhausted approaches
- Questions that scored 0% but were rejected by the owner (questions 16, 34, 38) are documented as "not meeting strawberry criteria" -- teaching the agent what success actually looks like

Over 104 iterations, the researcher learned to pivot from:
- Character counting (iterations 1-10) to relational logic (iterations 6-8)
- Self-referential paradoxes (iterations 12-16) to modification blindness (iterations 37-103)

### 7. Constitutional governance (implicit)

The system maintains alignment through:

- **Fixed success criteria**: < 10% consensus among 5 independent agents (never changes)
- **Strawberry criteria**: Questions must be simple enough for a child to answer (enforced by owner review of 0% scores)
- **Agent boundaries**: Answer agents cannot see other answers. The verifier cannot influence the researcher. Isolation prevents gaming.

### 8. MCP tool integration (research capability)

The `opus-researcher` agent uses MCP servers to discover real-world LLM failures:

| MCP Server | Purpose |
|-----------|---------|
| **Tavily Web Search** | Academic papers, blog posts about LLM failures |
| **Reddit MCP** | Community-discovered LLM failure cases |

Research findings from each iteration are saved to `mcp-tavily-findings.md` and `mcp-reddit-findings.md` within each research folder, creating an audit trail of external knowledge integrated into the loop.

---

## Architecture mapping to course concepts

| Course concept | Implementation in this repo |
|---------------|---------------------------|
| **Ralph Wiggum pattern** (Module 5.4) | `ralph.sh` -- bash loop spawning fresh Claude instances |
| **State machine workflow** (Module 1) | `research-workflow-state.yaml` -- resumable six-phase execution |
| **Accumulating intelligence** (Module 4) | `opus-researcher.md` grows with findings each iteration |
| **Binary backpressure** (Module 6) | Consensus score < 10% = stop, >= 10% = continue |
| **LLM-as-judge** (Module 6.3) | `opus-verifier` reads 5 answers, produces structured score |
| **Multi-agent coordination** (Module 3) | 5 parallel answer agents + 1 verifier for consensus |
| **Memory spectrum** (Module 4.2) | Implicit (previous-research-summary) to explicit (agent file) |
| **Dead-end pivoting** (Module 5.2) | Accumulated findings steer away from exhausted approaches |
| **Constitutional governance** (Module 2) | Fixed success criteria, strawberry criteria, agent isolation |
| **Defense-in-depth** (Module 7) | Agent isolation, owner review of 0% scores, iteration caps |

---

## Key files

| File | Role |
|------|------|
| `ralph.sh` | Outer loop runner (Ralph Wiggum pattern) |
| `prompt.md` | Fixed prompt for each iteration |
| `.claude/commands/execute-workflow.md` | State machine orchestrator |
| `.claude/commands/research-novel-question.md` | Generate phase |
| `.claude/commands/verify-novel-question.md` | Verify phase |
| `.claude/commands/commit-research.md` | Commit phase |
| `.claude/agents/opus-researcher.md` | Self-evolving researcher agent (1,549 lines) |
| `.claude/agents/opus-answer.md` | Simple answer agent (18 lines, used 5x) |
| `.claude/agents/opus-verifier.md` | Consensus verifier agent |
| `research/research-workflow-state.yaml` | Workflow state (resumable) |
| `research/research-questions.yaml` | All 104 questions with scores |
| `research/research{N}/` | Per-iteration research output |

---

## LLM limitations targeted

The research systematically explored five categories of fundamental LLM weakness:

| Limitation | Description | Result |
|-----------|-------------|--------|
| **Tokenization blindness** | LLMs process tokens, not characters -- cannot reliably count letters | Most questions scored 100% (LLMs have improved) |
| **Semantic priming** | Word meaning overrides the task (ELEVEN activates 11, not 6 letters) | Inconsistent results |
| **Pattern matching** | LLMs predict what answers look like rather than computing them | Moderate success (40-80% scores) |
| **Modification blindness** | Pattern-match to famous puzzles without noticing inversions | Winner -- 0% consensus on modified shadow riddle |
| **Relational logic** | Complex family/sibling relationships cause counting errors | Some success (80% scores) |

The winning question (#103) exploits **modification blindness**: LLMs recognize the classic "shadow" riddle structure but fail to notice that the condition is inverted (LONGER near the sun, not shorter). All 5 independent agents answered "shadow" -- the correct answer is your trail/path/journey.

---

## Running the system

### Single iteration

```bash
# Inside Claude Code
/execute-workflow
```

### Autonomous loop

```bash
# From terminal -- runs up to N iterations
./ralph.sh 100
```

### Prerequisites

- Claude Code CLI installed
- MCP servers configured (Tavily API key in `.mcp.json`)
- Git configured for auto-push

---

## Results summary

- **104 questions tested** over 200+ git commits
- **1 winning question** at 0% consensus (Question #103)
- **3 rejected winners** at 0% (Questions 16, 34, 38 -- did not meet strawberry criteria)
- **2 partial successes** at 40% (Questions 55, 104)
- The researcher agent evolved from basic character-counting tricks to sophisticated cognitive exploits targeting modification blindness

---

## Sources

The claims in this report rest on two bodies of public work: (1) the academic / industry literature defining "self-evolving agents," and (2) the documented Ralph Wiggum pattern as it has crystallized in the Claude Code ecosystem.

### Self-evolving agent definitions and frameworks
- [Self-Evolving Agents — A Cookbook for Autonomous Agent Retraining (OpenAI Cookbook)](https://cookbook.openai.com/examples/partners/self_evolving_agents/autonomous_agent_retraining) — defines the closed-loop, feedback-driven self-modification pattern this repo implements via the `update_agent` phase.
- [Self-Evolving Agents: Three Frameworks That Let Your AI Improve Itself (Softmax)](https://softmaxdata.com/blog/evo/) — surveys the design space; this repo's accumulating-knowledge approach maps to the "prompt/agent-definition mutation" branch (no model retraining).
- [How to Build a Self-Improving AI Agent That Learns From Its Own Mistakes (MindStudio)](https://www.mindstudio.ai/blog/self-improving-ai-agent-feedback-loop) — describes the run -> score -> diagnose -> modify cycle that matches this repo's six-phase state machine.
- [Self-Evolving AI Agents (Emergent Mind)](https://www.emergentmind.com/topics/self-evolving-ai-agent) — academic-leaning overview of the term as used in the agentic-AI literature.
- [Recursive Self-Improvement / Self-Play (Inferensys glossary)](https://inferensys.com/glossary/agentic-cognitive-architectures/recursive-self-improvement/self-play) — situates this repo's adversarial self-play (LLMs probing LLMs) within the broader recursive-self-improvement family.

### Agent loop architecture (general background)
- [What Is the AI Agent Loop? The Core Architecture Behind Autonomous AI Systems (Oracle)](https://blogs.oracle.com/developers/what-is-the-ai-agent-loop-the-core-architecture-behind-autonomous-ai-systems)
- [The Agent Loop: How AI Thinks, Decides, and Learns From Action (Tredence)](https://www.tredence.com/blog/ai-agent-loop)
- [Agent Loop Explained: Designing Smarter Agentic AI Solutions (TechAhead)](https://www.techaheadcorp.com/blog/understanding-the-agent-loop/)

### Ralph Wiggum pattern (inner loop engine)
- [Ralph Wiggum plugin — Anthropic Claude Code (official)](https://github.com/anthropics/claude-code/tree/main/plugins/ralph-wiggum) — Anthropic's reference implementation; validates `ralph.sh` as a textbook instance of the pattern.
- [Ralph Loop — Claude Plugin (Anthropic)](https://claude.com/plugins/ralph-loop) — official plugin listing.
- ['Ralph Wiggum' loop prompts Claude to vibe-clone software (The Register, Jan 2026)](https://www.theregister.com/2026/01/27/ralph_wiggum_claude_loops/) — mainstream coverage of the pattern's emergence.
- [The Ralph Wiggum technique: Run Claude Code autonomously for hours (Cyrus)](https://www.atcyrus.com/stories/ralph-wiggum-technique-claude-code-autonomous-loops) — practitioner write-up describing the self-correcting reinjection mechanic this repo uses.
- [Ralph Wiggum: Autonomous Loops for Claude Code (paddo.dev)](https://paddo.dev/blog/ralph-wiggum-autonomous-loops/) — origin attribution to Geoffrey Huntley and the "while true" formulation.
- [Ralph Wiggum Loop: Autonomous Iteration Workflows (AI Agent Factory)](https://agentfactory.panaversity.org/docs/General-Agents-Foundations/general-agents/ralph-wiggum-loop) — pedagogical treatment of the pattern.
- [Ralph Wiggum — AI Loop Technique for Claude Code (Awesome Claude)](https://awesomeclaude.ai/ralph-wiggum) — community catalog entry.

### Related implementations (sibling systems)
- [karpathy/autoresearch (GitHub)](https://github.com/karpathy/autoresearch) — the autonomous overnight research-loop pattern applied to LLM training; cross-analyzed in `karpathy-autoresearch-analysis.md` in this directory.
