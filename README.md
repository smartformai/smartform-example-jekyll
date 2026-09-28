# Jekyll contact form — Formspree alternative with AI spam filtering

Wire a contact form to [SmartForm AI](https://usesmartform.com) from a Jekyll site.

## What you're POSTing

The endpoint accepts a standard HTML form POST or JSON via AJAX. Two
kinds of fields:

**Your form fields** — `name`, `email`, `message`, whatever you
want. Every non-reserved field lands in your dashboard as a column in
the submissions table.

**Reserved fields** — names starting with `_` are interpreted by
the API, not stored:

| Field | Purpose |
|---|---|
| ``_gotcha`` | **Honeypot.** Keep it empty. Hidden from humans via CSS; bots fill it automatically. Any non-empty value silently drops the submission. Add this to every form. |
| ``_hp_email`` / ``_website`` / ``_url`` / ``_phone`` | Honeypot aliases for `_gotcha` (WordPress / WPForms / Contact Form 7 migrations). Same drop semantics. |
| ``_next`` | Same-origin URL to redirect to after a successful submission. Browser POST results in a 302 here. AJAX calls (with `Accept: application/json`) get the same value back as `next_url` in the JSON response. Only http(s) and in-site paths allowed. |
| ``_subject`` | Override the AI-generated email subject line. Max 200 chars; control characters stripped. |
| `X-Gotcha` header | Same as `_gotcha` for JSON requests where you can't add a hidden form field. |

Field names are Formspree-compatible — migrating from
`formspree.io/f/{form_id}` requires no renaming.

## Setup

1. Get a form ID at https://usesmartform.com/dashboard (8 chars, e.g. `f_abc12345`).
2. Clone, install, configure, run:
   ```bash
   git clone https://github.com/yanghuai123456/smartform-example-jekyll.git
   cd smartform-example-jekyll
   bundle install
   bundle exec jekyll serve
   ```
3. Open http://localhost:4000, submit, check your dashboard.

Add the form ID by editing `_config.yml`:
```yaml
smartform_form_id: f_your_real_id
```

## The form

`_includes/contact_form.html` is a pure HTML form posting to SmartForm's public endpoint.
Drop it into any layout with `{% include contact_form.html %}`.

```html
<form action="{{ site.smartform_form_id | prepend: 'https://api.usesmartform.com/api/v1/f/' }}" method="POST">
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
bundle exec jekyll build    # static output in ./_site
# Push ./_site to Netlify / Cloudflare Pages / GitHub Pages
```


## FAQ

### Is there a free tier?

Yes. AI spam filtering is enabled by default on every plan. AI intent
classification and high-value lead detection require a paid plan (Pro
or Business) — the dashboard enforces this and returns HTTP 402 if
you try to enable them on a free workspace.

### Do I need an API key?

No. The form posts directly to a public endpoint using only an 8-char
form ID, which is non-enumerable. The example also includes a hidden
`_gotcha` honeypot field so naive bots cannot submit.

### Does it work with GitHub Pages?
Yes. Jekyll builds static HTML and the form posts straight from the browser to the public endpoint. Deploy with the standard GitHub Pages pipeline.

## Related examples
[Astro contact form](https://github.com/yanghuai123456/smartform-example-astro) | [Hugo contact form](https://github.com/yanghuai123456/smartform-example-hugo) | [Gatsby contact form](https://github.com/yanghuai123456/smartform-example-gatsby)


## License

MIT.

