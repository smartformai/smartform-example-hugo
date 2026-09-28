# Hugo contact form — Formspree alternative with AI spam filtering

Wire a contact form to [SmartForm AI](https://usesmartform.com) from a Hugo site.

## Setup

1. Get a form ID at https://usesmartform.com/dashboard (8 chars, e.g. `f_abc12345`).
2. Clone, configure, run:
   ```bash
   git clone https://github.com/yanghuai123456/smartform-example-hugo.git
   cd smartform-example-hugo
   # edit hugo.toml → [params] smartformFormId = "f_your_real_id"
   hugo server
   ```
3. Open http://localhost:1313, submit, check your dashboard.

## The form

`layouts/partials/smartform.html` is a pure HTML form posting to SmartForm's public endpoint.
Drop it into any template with `{{ partial "smartform.html" . }}`.

```go-html-template
<form action="{{ printf "https://api.usesmartform.com/api/v1/f/%s" $.Site.Params.smartformFormId }}" method="POST">
  <input  name="name"    required />
  <input  name="email"   type="email" required />
  <textarea name="message" required></textarea>
  <input  type="text" name="_gotcha" tabindex="-1" autocomplete="off"
          style="position:absolute;left:-9999px" aria-hidden="true" />
  <button type="submit">Send</button>
</form>
```

The `_gotcha` field is a honeypot — bots fill it, humans never see it, SmartForm silently
discards those submissions.

## How the API works

- `POST https://api.usesmartform.com/api/v1/f/{form_id}` — JSON or form-data, no API key.
- Response: `{ success, message, submission_id, is_spam, intent, next_url }`.

For the full contract, see https://usesmartform.com/docs.

## Deploy

```bash
hugo                     # static output in ./public
# Push ./public to Netlify / Cloudflare Pages / GitHub Pages
```

## License

MIT.
