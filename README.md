# Session Handoff — Claude Code Skill

A [Claude Code](https://docs.anthropic.com/en/docs/claude-code) custom skill that captures your current session context into a portable `HANDOFF.md` file, so the next session — or another workstation — can pick up right where you left off.

## The Problem

Long projects outlive a single Claude Code session. When you hit your token limit or switch machines, the next session starts from scratch with no idea what you were doing, what you decided, or what's left to do. Rebuilding that context manually is tedious and error-prone.

## The Solution

Type `/session-handoff` and the skill automatically reads your git state, combines it with what it knows from the conversation, and writes a structured `HANDOFF.md` to your project root. The next session can read it in seconds.

## Example Output

```markdown
# Session Handoff

**Date:** 2026-03-09
**Branch:** feature/user-auth

## Summary

Implemented JWT-based authentication with refresh token rotation.
CLAUDE.md exists at project root with architectural guidelines.

## What Was Accomplished

- Added refresh token rotation endpoint at /api/auth/refresh
- Updated auth middleware to validate token expiry
- Wrote integration tests for token lifecycle

## Key Decisions

- Chose single-use refresh tokens over sliding window to limit
  exposure from compromised tokens
- Preserved legacy auth behind LEGACY_AUTH feature flag for
  gradual rollout

## Current State

Auth flow is functional end-to-end. Tests passing locally.

**Modified files:**
src/auth/refresh.ts, src/middleware/auth.ts, tests/auth.test.ts

**Staged changes:** All changes staged, not yet committed
**Stash entries:** None

## Next Steps

- Add rate limiting to the refresh endpoint (src/auth/refresh.ts)
- Update API docs in docs/auth.md
- Remove LEGACY_AUTH flag after rollout confirmation

## Relevant Files

- src/auth/refresh.ts — new refresh token rotation logic
- src/middleware/auth.ts — updated token validation
- tests/auth.test.ts — integration tests for auth flow
- docs/auth.md — API documentation (needs updating)
```

## Installation

Claude Code discovers skills from a `skills/` directory containing a named subfolder with a `SKILL.md` file. You can install at two scopes:

### Personal (available in all your projects)

```bash
# Clone the repo
git clone https://github.com/robsheppard/Claude-Skill-Session-Handoff.git

# Copy into your personal skills directory
mkdir -p ~/.claude/skills/session-handoff
cp Claude-Skill-Session-Handoff/SKILL.md ~/.claude/skills/session-handoff/SKILL.md
```

### Project-level (available only in a specific project)

From your project root:

```bash
mkdir -p .claude/skills/session-handoff
cp /path/to/SKILL.md .claude/skills/session-handoff/SKILL.md
```

You can commit the `.claude/skills/` directory to share the skill with your team.

Start a new Claude Code session to pick up the skill.

## Usage

Once installed, invoke the skill at any point during a session:

```
/session-handoff
```

The skill will:

1. **Gather context automatically** — reads your current branch, recent commits, working tree status, stash entries, and recently modified files
2. **Draw on conversation memory** — captures what you worked on, discussed, and decided during the session
3. **Ask once** — gives you a chance to add goals, decisions, or blockers, but accepts "no" without follow-up questions
4. **Write `HANDOFF.md`** — creates or overwrites the file at your project root

To continue on the same machine, just start a new session and point Claude to the file. To continue on a different workstation, push the file with your repo or copy it over directly.

## What It Captures

- Session summary and date
- What was accomplished
- Key decisions and rationale
- Current git state (modified files, staged changes, stash entries)
- Known issues or blockers
- Concrete next steps
- The most relevant files for continuing the work

The output stays under 150 lines and is written to be skimmable — a new session can get oriented in under 30 seconds.

## Customization

Fork this repo and edit `SKILL.md` to tailor the skill to your workflow. The file has two parts:

- **YAML frontmatter** (between `---` delimiters) — the `name` field sets the slash command and `description` controls when Claude auto-suggests the skill
- **Markdown body** — the prompt that defines what context to gather, the interview question, and the output format

For example, you could add project-specific sections, change the output location, or include additional git commands for monorepo setups.

## License

[MIT](LICENSE)
