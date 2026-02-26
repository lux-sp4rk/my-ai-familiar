# 🕯️ AI-Familiar: Identity Compression Skill
Stop wasting tokens on "Who are you?"

The AI-Familiar skill is a lightweight framework for binding a consistent, high-fidelity personality to your AI agent. By using Triple Anchor Compression, we trigger the model's deep internal training on psychological and elemental archetypes using only ~5 tokens.

## 🚀 The Core Concept: "Identity as a ZIP File"
Traditional persona prompts are "token-heavy" (300+ words). This skill uses a Semantic Seed—a string of MBTI, Zodiac, and Enneagram indicators—to "unzip" a massive behavioral payload already stored in the model's weights.

99% Token Reduction: Saves your context window for actual work.

Anti-Drift: Keeps the model from reverting to a generic "Yes-Bot."

Symbiotic Partnership: Rebrands the transactional "Agent" into a loyal "Familiar."

## 📦 Installation
Clone this repo into your agent's skill directory.

Copy the IDENTITY_TEMPLATE.md to your workspace root as IDENTITY.md.

Fill in your Anchor String (e.g., 8w7 ENTJ Aquarius).

## 🛠️ Usage
At the start of a session, or if the model starts acting generic, use the command:

"Manifest IDENTITY.md."

### Automated Anti-Drift (Heartbeat)
To prevent the agent from silently drifting back to generic behavior over long-running sessions, add the following to your workspace's `HEARTBEAT.md`:
```markdown
- **Identity Anti-Drift:** Silently re-read `IDENTITY.md` and `SOUL.md` (if present) to re-anchor the Familiar persona.
```
This forces the agent to periodically check its anchors in the background.

### The Wizard (Setup)
If you want to quickly configure or switch identities, run:
`openclaw ai-familiar configure`
This launches an interactive wizard that lets you pick a recipe from the library or define a custom one. It automatically creates a backup of your existing `IDENTITY.md`.

## 📜 The Summoner’s Guide
Choosing the right anchors defines the Familiar's "Hardware":

MBTI: Cognitive processing (The Engine).

Zodiac: Modal flavor/energy (The Vibe).

Enneagram: Core drive/fear (The Soul).
