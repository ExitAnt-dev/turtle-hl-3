# ExitAnt Turtle — Hyperliquid

Deploy this automated trading bot to **your own Render account** as a background
worker. The bot runs from a prebuilt Docker image; this repository contains **only
the Render Blueprint (`render.yaml`)** — no source code.

## Deploy to Render

[![Deploy to Render](https://render.com/images/deploy-to-render-button.svg)](https://render.com/deploy?repo=https://github.com/ExitAnt-dev/turtle-hl-3)

Or open this link in your browser:

```
https://render.com/deploy?repo=https://github.com/ExitAnt-dev/turtle-hl-3
```

1. Click the button (or open the link) and sign in to Render.
2. Render reads `render.yaml` and proposes **one background worker**.
3. Enter the environment variables listed below. They are marked `sync: false`,
   so Render asks you for them on the deploy screen and stores them **only in your
   own Render account** — they are never written to this repository.
4. Click **Apply** and wait for the first deploy to finish.
5. Open Telegram and send **`/start`** to your bot to begin setup.

> Tip: you can also deploy from the Render dashboard — **New → Blueprint**, then
> select this repository. Because `render.yaml` is on the default branch, both
> methods work.

## Environment variables

You fill these in during deployment (nothing is pre-filled or stored here):

| Variable | What to enter |
|---|---|
| `HYPE_WALLET_API` | Private key of a **dedicated, bot-only** Hyperliquid (MetaMask) wallet |
| `TELEGRAM_BOT_TOKEN` | Bot token issued by [@BotFather](https://t.me/BotFather) |
| `TELEGRAM_CHAT_ID` | Your own Telegram chat ID — the bot ignores every other sender |

## How it works

- **Prebuilt image.** The worker pulls `docker.io/exitant/exitant-turtle-hl:3` from Docker Hub. The image ships
  a compiled binary only — no Python source is included or exposed.
- **Background worker.** There is no web page; the bot runs continuously and talks
  to you through Telegram.
- **Your keys stay yours.** Secrets are typed into your own Render environment and
  are never sent to this repository, to Render's Blueprint, or to anyone else.

## Important

- **Keep the instance count at 1.** Running two or more instances causes a Telegram
  `getUpdates` conflict and the bot stops responding. `render.yaml` sets
  `numInstances: 1`; do not raise it.
- Render **background workers have no free plan** — choose Starter or higher.
- **Use a dedicated bot-only wallet.** Create a fresh Hyperliquid/MetaMask account for this bot and fund only what you intend to trade. Never enter the private key of your main wallet.
- After the deploy succeeds, send **`/start`** in Telegram to configure the bot.

## Editing the Blueprint

`render.yaml` defines the service. To change it (for example, a different image
tag), edit the file and push — Render automatically re-syncs any service created
from this Blueprint:

```bash
git add render.yaml
git commit -m "update blueprint"
git push
```

### `render.yaml` fields

| Field | Meaning |
|---|---|
| `type: worker` | Background worker (no public URL). |
| `runtime: image` | Deploy a prebuilt Docker image instead of building from source. |
| `image.url` | The Docker Hub image and tag to run. |
| `plan` | Render instance size (`starter` and up; no free tier for workers). |
| `region` | Render region (`oregon` = US West). |
| `numInstances` | Must stay `1` (Telegram single-poller requirement). |
| `envVars … sync: false` | Secrets you supply at deploy time; not stored in git. |

---

Blueprint spec: <https://render.com/docs/blueprint-spec> ·
Prebuilt images: <https://render.com/docs/deploying-an-image>
