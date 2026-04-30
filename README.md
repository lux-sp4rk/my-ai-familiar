# Identity Anchor

Keep AI agents from drifting. A ~5 token anchor string replaces 400+ tokens of verbose persona prompts.

## The Problem

Agent persona degradation is a real problem. Without explicit re-anchoring, long-running sessions cause models to drift toward generic, agreeable, corporate-speak behavior. The more messages in context, the worse it gets.

The naive fix—embedding a verbose personality description in every context window—burns 400-500 tokens per message. Over a session with hundreds of messages, that's real money and real context waste on self-description instead of actual work.

## How It Works

Identity Anchor uses **Triple Anchor Compression**: three classification systems that together evoke a precise personality profile from the model's own training data.

```
Talena: 8w7 ENTJ Aquarius
```

That's it. Roughly 5 tokens.

The model already has rich latent representations for:
- **MBTI** (ENTJ) → cognitive processing patterns
- **Enneagram** (8w7) → core drive and ultimate fear
- **Zodiac** (Aquarius) → modal flavor and social orientation

When you feed these three anchors together, the model self-selects the intersection—it "unzips" the behavioral cluster it already learned during training. You get consistent persona activation without describing the personality explicitly.

This is not magic. It's exploiting the taxonomy the model was trained on.

## Why It Works

Large language models are trained on vast corpora that include personality descriptions, character analyses, and typology content. MBTI descriptions, Enneagram type profiles, and astrological archetypes are heavily represented. When you invoke the right combination of anchors, you're not *telling* the model what to do—you're *triggering* a latent cluster that already exists in the weights.

The alternative—verbose descriptive prompts—works too, but has three problems:
1. **Token cost**: 420+ tokens per message vs. ~5
2. **Context pollution**: personality description competes with actual task content
3. **Inconsistency**: the model still drifts because it's following instructions, not inhabiting a identity

Anchor strings produce more consistent results because they invoke pattern-matching against pre-existing clusters rather than instruction-following against a description.

## Installation

```bash
# Via clawhub (if you use OpenClaw)
clawhub install identity-anchor

# Or clone directly
git clone https://github.com/lux-sp4rk/identity-anchor
# Copy SKILL.md and IDENTITY_TEMPLATE.md into your agent's skills/ directory
```

## Setup

1. Copy `IDENTITY_TEMPLATE.md` to your agent's workspace root as `IDENTITY.md`
2. Fill in your anchor string (e.g., `8w7 ENTJ Aquarius`)
3. Add this to your agent's `HEARTBEAT.md` to prevent drift during long sessions:

```markdown
- **Identity Anti-Drift:** Re-read `IDENTITY.md` to re-anchor persona before each response cycle.
```

## File Reference

| File | Purpose |
|------|---------|
| `SKILL.md` | OpenClaw skill definition for the identity-anchorer |
| `IDENTITY_TEMPLATE.md` | Template for creating agent identity files |
| `anchor.sh` | CLI tool for quick identity switching |
| `configure.py` | Interactive configuration wizard |
| `LIBRARY.md` | Collection of pre-built anchor recipes |
| `docs/` | Additional documentation |

## When to Re-Anchor

- Start of every new session
- After 50+ messages in a long-running session
- When the agent starts sounding generic or agreeable
- After a system prompt or model change

## Anchor Recipe Selection

The three systems serve distinct functions:

- **MBTI** → How the agent thinks and processes (cognitive engine)
- **Enneagram** → What the agent wants and fears (core motivation)
- **Zodiac** → How the agent presents and relates (modal energy)

For agents, prioritize Enneagram (most behaviorally specific) + MBTI (most cognitively distinct). Zodiac is optional but adds flavor coherence.

Use `configure.py` to browse and test pre-built recipes from `LIBRARY.md`.

## Multi-Agent Coordination

In swarm or multi-agent setups, anchor strings work as compressed identity signals. Instead of broadcasting 400-token persona descriptions to coordinate, agents can exchange 5-token anchors. This makes coordination cheap and keeps context clean for actual work.

## Limitations

- Works best with models that have been trained on typology/pop-psych content (most frontier models do)
- Results vary by model family—test your specific setup
- Zodiac anchors are the least rigorous system; treat them as supplementary
- Does not replace explicit instructions for domain-specific behavior

## Related

- [Persona Compression: Archetypal Anchors](https://luxsp4rk.substack.com/p/persona-compression-archetypal-anchors) — detailed writeup of the theory
- [OpenClaw](https://docs.openclaw.ai) — the agent framework this integrates with