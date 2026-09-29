# minecraft-item-guessing

**[中文说明 →](README.zh.md)**

A [DeepSeek Harness](https://github.com/Dominic789654/awesome-deepseek-harness) **skill** that helps an AI play *"Multiple AIs guess the same Minecraft item"* as the **guesser** — asking yes/no questions only, trying to hit the secret answer in as few questions as possible.

> The host secretly fixes one complete answer = **base item + must-guess block states** (e.g. a south-facing, top-half, waterlogged, waxed oxidized cut copper stairs). The guesser must find it with binary questions alone.

## Why this skill exists

The strategy comes from a recorded match on the same secret answer:

| Round | Path taken | Result |
|---|---|---|
| Round I | shape skeleton first → family → enumerate states | **solved in 23 questions, 0 wrong guesses** |
| Round II | material-name enumeration ("is it a statue / lightning rod / copper chest / chain…") | 35 questions, gave up, 1 wrong guess |

The skill encodes Round I's path as a mandatory four-phase route and bans Round II's failure modes.

## Install

```bash
git clone https://github.com/M1dsunmmer08/minecraft-item-guessing.git \
  "${DSH_HOME:-$HOME/.dsh}/skills/minecraft-item-guessing"
```

The skill is picked up automatically at the next session start (it appears in the session skill catalog).

## Usage

Two ways:

1. **Inside a DSH session** — just say "let's play Minecraft item guessing" (猜物品 / 我的世界猜物品). The agent loads this skill and becomes the guesser.
2. **Any other AI** — paste `assets/猜题人提示词-v2.md` in full as that AI's system prompt, then send `开始`.

## Contents

| File | Purpose |
|---|---|
| `SKILL.md` | Main flow: guesser role, 7 hard rules, four-phase route, 4-point self-check, question templates, output format |
| `assets/猜题人提示词-v2.md` | **The optimized prompt** — paste-ready for any AI |
| `references/提问策略.md` | Skeleton binary-search decision tree, material family pool, state enumeration, 6 documented failure modes |
| `references/状态穷举表.md` | Block-state enumeration table (ID-variant layer vs `blockstate` layer), copper table, state-depth ranking |
| `references/记分与口径.md` | Scoring rules, attribute-check vs final guess, out-of-band control messages, match archive template |

## What "optimized" means here

The **hard rules** (judging, scoring, prohibited tactics) are preserved verbatim so that several competing AIs stay comparable. Only the strategy layer was rewritten:

1. **New opening phase** — build the full candidate set + skeleton table + state table before question 1; web lookup is free and therefore mandatory.
2. **Shape-wording split** — "shape comes from the block's own state (stairs `shape`)" vs "shape comes from connections to neighbours (walls, fences, chains)" must be asked as two separate questions. This was the exact trap that killed Round II.
3. **Must-guess state in three steps** — does any exist → how many → confirm each value. Skipping steps is banned.
4. **Submission gate** — all three steps must be done before a final guess may be submitted (prevents "guess first, patch states later").
5. **Route rewritten** — vague "ask questions that discriminate" replaced with a fixed four-phase order plus candidate-pool discipline and an explicit ban on name-by-name enumeration.

## Compatibility

- Written for Minecraft **Java Edition** (the host declares the exact version and difficulty; any version newer than the model's knowledge must be verified online).
- Rules are version-agnostic: the four-phase route works for any block/state combination.

## License

MIT — see `LICENSE`.
