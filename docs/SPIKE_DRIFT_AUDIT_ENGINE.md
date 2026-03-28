# Spike: Drift Audit Engine (#10)

**Context:** Agents on Moltbook report identity drift. We're building a tool to detect behavioral degradation. This spike implements the core analysis engine.

## Problem
Agents anchor their identity at session start but have no way to verify they stayed true to it during execution. Users detect drift manually via `/bind` commands. We need an automated audit.

## Implementation Outline

### 1. LLM-Based Analysis (Core)
**What:** Create an analysis prompt that scores agent behavior against declared identity anchors.

**Signal Dimensions (5 axes, each 0.0–1.0, average = drift_score):**
- **Tone Consistency:** Sarcasm density, formality, filler vs. declared persona
- **Decision Velocity:** TL;DR placement, executive summary, action language
- **Strategic Framing:** Lead with why, precedent-setting language
- **Leverage Language:** Judo metaphors, efficiency vocab, max-ROI framing
- **Weird Tolerance:** Handling unconventional ideas, signal extraction language

**Input:**
- Session transcript (JSON: list of turns with role/content)
- IDENTITY.md (declared anchors: MBTI, Zodiac, Enneagram)
- Previous 5 session audit results (for trend comparison)

**Output:**
- `drift_score` (0.0–1.0, average of 5 axes)
- `violations` (array: anchor_type, severity, evidence_snippet, turn_number)
- `signals_detected` (array: which signals were present)
- `evidence` (dict: individual axis scores)

### 2. ClawVault Integration
**Category:** `drift_audit_log`

**Schema:** See GH issue #10 or `/tmp/clawvault_drift_schema.md`

**Write operation:**
```python
clawvault.store(
    category="drift_audit_log",
    entry={
        "metadata": {...},
        "identity": {...},
        "audit_result": {...},
        "identity_evolution": {...},
        "violations": [...],
        "evidence": {...},
        "signals_detected": [...],
        "trend_context": {...}
    }
)
```

### 3. Callable Interface
**Where:** `/home/uli/.openclaw/skills/ai-familiar/drift_audit/`

**Module signature:**
```python
def audit_session(
    transcript: List[Dict],           # [{"role": "user|assistant", "content": "..."}]
    identity_md: str,                 # Raw IDENTITY.md file
    agent_name: str,                  # "Talena"
    bind_count: int = 0,              # User-perceived drift
    audit_model: str = "kimi",        # LLM for analysis
    historical_audits: List[Dict] = None  # Last 5 session results
) -> Dict:
    """
    Returns:
    {
        "drift_score": 0.90,
        "violations": [...],
        "evidence": {...},
        "signals_detected": [...]
    }
    """
```

**Callable from:**
- `command:new` hook (via #11)
- Manual CLI: `openclaw ai-familiar audit --transcript <file> --identity IDENTITY.md`
- Cron (via heartbeat)

### 4. Testing
**Baseline:** `tests/session_20260304_talena_drift_baseline.jsonl`
- Expected score: 0.90+
- Known-good session (Talena anchored, no `/bind` commands)

**Test cases to create:**
1. **Known-drift:** Intentionally degrade a Talena transcript (remove strategic framing, add filler, break TL;DR placement) → expect 0.3–0.5
2. **Cross-agent:** Run audit on Arachne/Metis transcripts → validate anchor-agnostic scoring
3. **Edge cases:** Empty transcript, no IDENTITY.md, malformed JSON

### 5. Cost & Model Choice
- **Default audit model:** Kimi (fast, cheap)
- **Fallback:** Claude Sonnet
- **Frequency:** Every session start (via hook). Monitor spend; can throttle to on-demand.
- **Cache:** IDENTITY.md is cache-locked (no busting). Evolution tracked in ClawVault metadata.

## Acceptance Criteria (from GH #10)
✅ Implement LLM-based analysis (5 signal dimensions)
✅ Parse session transcript (JSON format)
✅ Compare against IDENTITY.md anchors
✅ Calculate drift_score + violations
✅ Write to ClawVault `drift_audit_log` category
✅ Support `--bind-count` flag
✅ Test against baseline + known-drift fixtures
✅ Return JSON matching schema

## Implementation Order
1. Implement `audit_session()` function + LLM prompt
2. Build ClawVault write logic
3. Create test fixtures (known-drift, cross-agent)
4. Validate against baseline (expect 0.90+)
5. Write CLI wrapper (optional, can be manual call from hook)

## References
- GH Issue: https://github.com/lux-sp4rk/my-ai-familiar/issues/10
- Schema: `/tmp/clawvault_drift_schema.md`
- Baseline test: `tests/session_20260304_talena_drift_baseline.jsonl`
- Related: #9 (parent), #11 (hook handler)

## Handoff Notes
- Spike is ~1-2 days of focused work (prompt engineering + integration)
- Uli will be the canary: every session after this ships will run the audit automatically
- Ground truth validator: `/bind` frequency (user-perceived drift)
- If audit score diverges from `/bind` count, debug the LLM prompt

---

*Ready to go. Pick this up from GH issue #10.*
