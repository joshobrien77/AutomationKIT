# n8n Live Demo — Automated Discord Poster

A 3-node automation that reacts to a schedule, pulls fresh data from a public API, and posts it to Discord on its own. Built to be demo-proof on stage (no OAuth, no credential that can hang).

---

## 🔑 Your Discord Webhook URL

Paste your webhook URL here so it's ready to copy on stage:

```
WEBHOOK_URL = 
```

> ⚠️ Treat this URL like a password — anyone with it can post to your channel.

---

## Part 1 — Create the Discord Webhook (do once, before stage)

1. In your Discord server, pick or create the channel for messages (e.g. `#n8n-demo`).
2. Hover the channel → click the **gear icon** (Edit Channel).
3. Open **Integrations** in the left menu.
4. Click **Webhooks** → **New Webhook**.
5. Name it (e.g. "n8n") and optionally set an avatar — this is the sender shown on stage.
6. Click **Copy Webhook URL** and paste it into the box above.
7. Click **Save Changes**.

---

## Part 2 — Build the Workflow (live on stage)

### Node 1 — Schedule Trigger
1. New workflow → click **Add first step** (or the **+** on the canvas).
2. Search **Schedule Trigger** and select it.
3. Set the interval to **Seconds → every 30** (keeps the pace lively for a live audience).
4. Close the panel.

### Node 2 — HTTP Request (get the data)
1. Click the **+** to the right of the trigger.
2. Search **HTTP Request** and select it.
3. Set:
   - **Method:** `GET`
   - **URL:** `https://icanhazdadjoke.com/`
   - **Headers** → add one header:
     - Name: `Accept`
     - Value: `application/json`
4. Click **Execute step** — a joke appears in the output panel.
   *(Good moment to narrate: "the data just showed up.")*

### Node 3 — HTTP Request (post to Discord)
1. Click the **+** after the previous node.
2. Search **HTTP Request** and select it.
3. Set:
   - **Method:** `POST`
   - **URL:** paste your **[WEBHOOK_URL]** (from the top of this file)
   - **Send Body:** ON → **Body Content Type:** `JSON`
   - **Body (JSON):**
     ```json
     { "content": "🤖 Auto-posted by n8n: {{ $json.joke }}" }
     ```
4. Click **Execute step** — the message lands in your Discord channel. 🎉

> **If n8n can't find `$json.joke`:** reference the dad-joke node explicitly —
> `{{ $('HTTP Request').item.json.joke }}` (use that node's exact name).

---

## Part 3 — The Automation Payoff
1. Top-right: flip the workflow toggle to **Active**.
2. Step back and deliver your line — *"I'm not touching anything."*
3. Every 30 seconds, a new joke posts to Discord hands-free. That's the automation moment.

### Optional mic-drop
While it's running, say *"let me make it smarter"*, drop an **Edit Fields (Set)** or **AI Agent** node between Node 2 and Node 3 to reformat the message, and re-run. Shows how easily it extends.

---

## API Swap-Ins (optional)

| Vibe | URL | Reference field |
|------|-----|-----------------|
| Dad jokes (default) | `https://icanhazdadjoke.com/` (Accept: application/json) | `{{ $json.joke }}` |
| Weather (Las Vegas) | `https://wttr.in/Las%20Vegas?format=3` | `{{ $json.data }}` |
| Hacker News top IDs | `https://hacker-news.firebaseio.com/v0/topstories.json` | needs formatting |

---

## ✅ Pre-Stage Checklist
- [ ] Webhook created and URL pasted above.
- [ ] Full workflow run end-to-end at least twice (ideally on the venue's network).
- [ ] Discord channel open on a second screen for the audience.
- [ ] One successful execution pinned as a visual fallback if wifi dies.
- [ ] Interval set to 30s **only right before** the talk (don't leave a loop hammering the API all day).
