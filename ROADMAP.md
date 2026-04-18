# Roadmap

## Leading requirements

1. **Website from this repo** — Deploy the static site in `cryptolir/1testweb` as a live website.
2. **Hosted Telegram bot running a Claude managed agent** — Host a Telegram bot that invokes managed agent `agent_011CZt85Kf5rfWvjRS9SWm8w`.
3. **Telegram-driven commits** — The agent receives instructions from a Telegram group and commits the resulting changes to this repo. CI/CD is already configured to deploy on commit.

## Status

### 1. Website deployment ✅
Live at http://gsagwomzpgebskpakv32qxyn.178.104.184.3.sslip.io
- [x] Static scaffold committed (`index.html`, `style.css`, `README.md`)
- [x] Repo `cryptolir/1testweb` created on GitHub (public)
- [x] Coolify app provisioned (`build_pack=static`, nginx:alpine)
- [x] Fixed broken GithubApp source binding — switched app to "Public GitHub" source via direct DB update (`source_id=NULL` on `applications`)
- [x] Updated repository field to full URL (`https://github.com/cryptolir/1testweb`)
- [x] Successful deploy → container running, HTTP 200

### 2. Bot + managed agent wiring ✅
- [x] `tg-agent-bot.service` (systemd) installed at `/opt/tg-agent-bot/`, env at `/etc/tg-agent-bot.env`, state at `/var/lib/tg-agent-bot/state.json`
- [x] Bridges one Telegram group ↔ one managed-agent session per chat
- [x] Anthropic vault `vlt_011CZtqev3zMkRfa8ynUigJ7` holds credential `vcrd_017bn8MpGv8CE9fxSh1hpMig` for MCP server `github` → `https://api.githubcopilot.com/mcp/`
- [x] GitHub fine-grained PAT rotated programmatically via `client.beta.vaults.credentials.update(...)` (2026-04-18)

### 3. Telegram → agent → commit → deploy ✅
Verified end-to-end 2026-04-18.
- [x] Bug: sessions created without `vault_ids` → MCP had no credential source. Fixed by adding `ANTHROPIC_VAULT_IDS` env and passing `vault_ids=[...]` to `sessions.create`
- [x] Bug: stream loop waited for event types `session.status.idle` / `session.status.terminated` (dot) while SDK emits `session.status_idle` / `session.status_terminated` (underscore) → stream hung, replies were silently dropped. Fixed by correcting event names
- [x] Confirmed a Telegram message reaches the agent, MCP initializes, and a commit can land on `main` which triggers Coolify redeploy

## Next

### 4. Bot only responds when addressed 🔜
Today the bot replies to every non-command text message in the allowed group. Change it so the bot only engages when explicitly addressed, to reduce noise and avoid accidental agent turns from unrelated group chatter.

Candidate triggers (pick one or combine):
- [ ] `@bot_username` mention in the message
- [ ] Reply to one of the bot's messages
- [ ] Message starts with a configured prefix (e.g. `claude,` or `!ask`)
- [ ] Direct-to-bot messages in private chat still always engage

Implementation sketch in `bot.py`:
- Fetch `bot.username` once at startup (via `app.bot.get_me()`).
- In `_handle_message`, before acquiring the chat lock, check whether the update meets any trigger condition; if not, `return` without invoking the agent.
- Strip the trigger (mention/prefix) from the text before forwarding to the agent.
- Keep `/start`, `/reset` commands working as today.

## Notes

- Repo: https://github.com/cryptolir/1testweb
- Managed agent id: `agent_011CZt85Kf5rfWvjRS9SWm8w`
- Environment id: `env_016kLbrLo7k164VQbaHF8MbS`
- MCP GitHub credential: vault `vlt_011CZtqev3zMkRfa8ynUigJ7`, credential `vcrd_017bn8MpGv8CE9fxSh1hpMig`
- Hosting: Coolify on same host; app source = Public GitHub (no auth needed)
- Bot service: `/opt/tg-agent-bot/bot.py`, env at `/etc/tg-agent-bot.env`
