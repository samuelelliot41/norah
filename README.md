[README.md](https://github.com/user-attachments/files/30631895/README.md)
# norah# Norah assistant — backend

One file, one job: `api/ask.js` takes a question, calls Claude with Norah's
knowledge as context, returns `{ answer, related }`. This is the only place
your Anthropic API key exists — the Framer widget never sees it.

## Deploy (10 minutes)

1. Get an API key at https://console.anthropic.com (Settings → API Keys).
2. Push this folder to a GitHub repo (or drag-and-drop it — Vercel supports
   both).
3. Go to https://vercel.com → **Add New Project** → import the repo.
4. Before the first deploy, add an environment variable:
   - Name: `ANTHROPIC_API_KEY`
   - Value: the key from step 1
5. Deploy. Vercel auto-detects `/api/ask.js` as a serverless function — no
   build config needed.
6. Your endpoint is now live at `https://<your-project>.vercel.app/api/ask`.

## Test it

```bash
curl -X POST https://<your-project>.vercel.app/api/ask \
  -H "Content-Type: application/json" \
  -d '{"question":"How much does Norah cost?"}'
```

You should get back:

```json
{
  "answer": "Solo is $99/month...",
  "related": ["What's included in Enterprise?", "..."]
}
```

## Before going live

- **Lock down CORS.** Right now `Access-Control-Allow-Origin` is `*` (any
  site can call your endpoint). In `api/ask.js`, change it to your real
  domain, e.g. `res.setHeader('Access-Control-Allow-Origin', 'https://norah.ai')`.
- **Keep KNOWLEDGE in sync.** Everything the assistant is allowed to say
  lives in the `KNOWLEDGE` string at the top of `api/ask.js`. Update it
  whenever pricing, setup steps, or integrations change on the real page.
- **Rate limiting.** This function has none. If you expect real traffic,
  add Vercel's rate limiting or a simple IP-based check before it ships
  publicly — otherwise anyone can hammer your endpoint and run up your
  Anthropic bill.
- **Model choice.** Uses `claude-sonnet-5`. For a simple FAQ widget like
  this, `claude-haiku-4-5-20251001` is cheaper and likely fast enough —
  worth testing both and comparing answer quality before deciding.
- **Pricing.** Check https://docs.claude.com for current API rates before
  estimating cost at volume — this changes over time.
