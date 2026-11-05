# haynes2e-public

Source for the Haynes Engineering & Excavation website (haynes2e.com): a plain static site (HTML/CSS/JS, no build tools) hosted on GitHub Pages.

## Layout

```
site/                     <- everything that gets published
  index.html              single-page site
  404.html
  assets/css/style.css
  assets/js/main.js       mobile menu + contact form
  assets/img/             logo, photos, icons
.github/workflows/
  deploy-staging.yml      demo   -> haynes2e/haynes2e-staging (gh-pages)
  deploy-production.yml   master -> this repo's GitHub Pages site
```

To preview locally, run `python3 -m http.server --directory site` and open http://localhost:8000.

## Branches and deployment

| Branch | Deploys to | Contact form |
|---|---|---|
| `demo` | https://haynes2e.github.io/haynes2e-staging/ | Disabled (no key) |
| `master` | GitHub Pages for this repo, later haynes2e.com | Live |

The workflow is:

1. Make changes on `demo`. Every push publishes to staging, with a yellow "STAGING PREVIEW" banner and `noindex`.
2. When the changes are approved, merge `demo` into `master` to publish production.

### Staging (already configured)

- `haynes2e-staging` publishes its `gh-pages` branch with GitHub Pages. That branch is overwritten on every deploy, so don't edit it by hand.
- `haynes2e-staging` has a deploy key with write access.
- This repo has an environment named `staging`, limited to the `demo` branch, with the secret `STAGING_DEPLOY_KEY` (the deploy key's private half).

### Production (to do when the owner approves)

1. Go to Settings → Pages → Source and choose **GitHub Actions**. This creates the `github-pages` environment.
2. Go to Settings → Environments → `github-pages`:
   - Under deployment branches, allow **`master` only**.
   - Add the secret **`WEB3FORMS_ACCESS_KEY`**.
3. Go to Settings → Secrets and variables → Actions → Variables and add **`PRODUCTION_ENABLED` = `true`**.
4. Merge `demo` into `master`, or re-run the "Deploy to production" workflow. The site goes live at `haynes2e.github.io/haynes2e-public/`.
5. Cutover to the custom domain:
   - Verify `haynes2e.com` under org Settings → Pages.
   - Set the custom domain under repo Settings → Pages.
   - Point DNS at GitHub Pages: A/AAAA records for the apex domain, and a CNAME from `www` to `haynes2e.github.io`.
   - Enable "Enforce HTTPS".

## Contact form

- The form posts to [Web3Forms](https://web3forms.com). No email address appears anywhere on the site.
- `index.html` contains the placeholder `__WEB3FORMS_ACCESS_KEY__`. Only the production workflow replaces it, using the environment secret. Without a real key, the form shows itself as disabled.
- Spam protection comes from a hidden honeypot field plus Web3Forms' own filtering. If spam becomes a problem, Web3Forms supports adding hCaptcha.
- The free plan allows 250 submissions a month. Above that, submissions are rejected and visitors see an error message with the phone number.

## Adding contracting credentials later

`index.html` contains a commented-out **Credentials** section with slots for UEI, CAGE, SAM.gov status, NAICS codes, Missouri license, bonding/insurance and certifications. It also has a link to a capabilities statement PDF, expected at `site/assets/docs/capabilities-statement.pdf`. To use it, uncomment the section and fill in the values.
