# Jekyll contact form — Formspree alternative with AI spam filtering

Wire a contact form to [SmartForm AI](https://usesmartform.com) from a Jekyll site.

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
## Related examples
[Astro contact form](https://github.com/yanghuai123456/smartform-example-astro) | [Hugo contact form](https://github.com/yanghuai123456/smartform-example-hugo) | [Gatsby contact form](https://github.com/yanghuai123456/smartform-example-gatsby)


## FAQ

### Why use this instead of Formspree?

Both SmartForm and Formspree let you POST a plain HTML form to a hosted
endpoint with no backend. SmartForm adds an AI spam filter (not just
honeypots), AI intent classification (`sales` / `support` / `inquiry`)
and high-value lead detection, with a free tier that includes the spam
filter. Formspree charges per submission; SmartForm's spam filter is
free on every plan.

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

