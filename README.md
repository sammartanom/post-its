# PUFSD DLC Living Questions

A Cloudflare Worker serving the interactive sticky-note experience and a grounded Workers AI endpoint.

## Dashboard + GitHub deployment

1. Put this folder in a GitHub repository.
2. In Cloudflare, open **Workers & Pages** and choose **Create application**.
3. Connect the GitHub repository and choose this project.
4. Use `npx wrangler deploy` as the deploy command if Cloudflare asks for one.
5. The included `wrangler.jsonc` defines the static asset directory, Worker entry point, and `AI` binding.
6. Deploy, then open `/api/health` on the assigned Worker domain to verify the Worker route.
7. Open the home page and use the **Ask** view. Answers are generated from retrieved note transcriptions and include clickable supporting-note references.

## Local development

```bash
npm install
npm run dev
```

## Architecture

- `public/index.html`: self-contained branded visual experience with the original image embedded.
- `src/index.js`: Worker router and grounded `/api/ask` endpoint.
- `src/notes.js`: structured transcription dataset.
- Workers AI model: `@cf/meta/llama-3.1-8b-instruct-fp8`.
- The Worker serves static assets through the `ASSETS` binding.

## API

`POST /api/ask`

```json
{ "question": "What concerns relate to AI and homework?" }
```

The endpoint retrieves relevant notes locally, sends only those notes to Workers AI, and returns the generated answer plus source-note metadata.

## Privacy and production notes

- The current dataset contains questions only and does not include author names.
- No question history is stored by this code.
- The Worker rejects empty requests, non-JSON requests, and questions longer than 500 characters.
- Before public launch, verify every transcription marked `draft` or `uncertain`.
- For a broadly public site, add Cloudflare Turnstile and rate limiting to reduce automated abuse and AI usage costs.
