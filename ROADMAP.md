# Roadmap

## Leading requirements

1. **Website from this repo** — Deploy the static site in `cryptolir/1testweb` as a live website.
2. **Hosted Telegram bot running a Claude managed agent** — Host a Telegram bot that invokes managed agent `agent_011CZt85Kf5rfWvjRS9SWm8w`.
3. **Telegram-driven commits** — The agent receives instructions from a Telegram group and commits the resulting changes to this repo. CI/CD is already configured to deploy on commit.

## Status

- [x] 1. Website deployment — live at http://gsagwomzpgebskpakv32qxyn.178.104.184.3.sslip.io (Coolify + nginx, auto-deploys from `main`)
- [x] 2. Telegram bot hosting + managed agent wiring — `tg-agent-bot.service` (systemd) relaying to agent `agent_011CZt85Kf5rfWvjRS9SWm8w`; MCP GitHub token rotated 2026-04-18
- [ ] 3. Telegram group → agent → git commit flow — plumbing in place, end-to-end commit not yet observed

## Notes

- Repo: https://github.com/cryptolir/1testweb
- Managed agent id: `agent_011CZt85Kf5rfWvjRS9SWm8w`
- Environment id: `env_016kLbrLo7k164VQbaHF8MbS`
- MCP GitHub credential: vault `vlt_011CZtqev3zMkRfa8ynUigJ7`, credential `vcrd_017bn8MpGv8CE9fxSh1hpMig`
- Hosting: Coolify on same host; app source = Public GitHub (no auth needed)
- Bot service: `/opt/tg-agent-bot/bot.py`, env at `/etc/tg-agent-bot.env`
