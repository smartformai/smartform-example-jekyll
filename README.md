# SmartForm + Jekyll

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

## License

MIT.
