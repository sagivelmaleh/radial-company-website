# Xordx website

The public website of [[LEGAL_NAME]]. Plain static HTML and CSS, hosted on Cloudflare Pages. No framework, no build step.

- **Live site:** https://[[DOMAIN]]
- **Working on it with Claude Code?** Read [`CLAUDE.md`](CLAUDE.md) first.

## Preview locally

You only need Python 3 (preinstalled on macOS and most Linux; on Windows get it from python.org).

```bash
cd path/to/this/repo
python3 -m http.server 8000
```

Open http://localhost:8000 in your browser. Press `Ctrl+C` in the terminal to stop. After editing a file, just refresh the browser.

Use the server rather than double-clicking `index.html`: the pages use root-relative paths like `/assets/css/styles.css`, which only resolve when served.

To check mobile layout, open your browser's developer tools (F12) and turn on the device toolbar.

## Day-to-day workflow

1. `git checkout main && git pull`
2. `git checkout -b my-change`
3. Edit, preview locally, commit.
4. `git push -u origin my-change`. Cloudflare posts a preview URL on the commit/PR in GitHub (also visible in the Cloudflare dashboard under the project's **Deployments**).
5. Check the preview on desktop and phone, open a pull request, merge to `main`. **Merging to `main` = live.**

---

## One-time setup

### 1. Create a private GitHub repo and push

1. On GitHub: **+** (top right) → **New repository**.
2. Name it (e.g. `company-website`), choose **Private**, and do **not** add a README, .gitignore or license (this repo already has them). Click **Create repository**.
3. In a terminal, inside this folder:

   ```bash
   git add -A
   git commit -m "Initial site"
   git remote add origin git@github.com:YOUR-ACCOUNT/company-website.git
   git push -u origin main
   ```

   (Use the `https://github.com/...` URL instead if you don't use SSH keys.)

### 2. Invite your partner

1. Repo → **Settings** → **Collaborators** (under "Access").
2. **Add people**, enter their GitHub username or email, and send the invite.
3. They accept the email invite, then clone: `git clone git@github.com:YOUR-ACCOUNT/company-website.git`.

On a free personal account, collaborators get write access, which is what they need.

### 3. Connect Cloudflare Pages

1. Create a free account at https://dash.cloudflare.com (if you don't have one).
2. **Workers & Pages** → **Create** → **Pages** tab → **Connect to Git**.
3. Authorize the **Cloudflare Pages** GitHub app. When asked which repositories it can access, choose **Only select repositories** and pick this repo.
4. Select the repo and click **Begin setup**. Use these settings:

   | Setting | Value |
   | --- | --- |
   | Project name | e.g. `company-website` (gives you `company-website.pages.dev`) |
   | Production branch | `main` |
   | Framework preset | **None** |
   | Build command | *(leave empty)* |
   | Build output directory | `/` |

5. **Save and Deploy**. After a minute the site is live at `https://<project-name>.pages.dev`.
6. Branch previews are on by default: every pushed branch gets `https://<branch-name>.<project-name>.pages.dev`. Check under the project's **Settings** → **Builds & deployments** → **Preview branches** that it's set to *All non-production branches*.

Optional: to keep preview URLs from being found by search engines or strangers, enable **Settings** → **General** → **Access policy** (Cloudflare Access) for previews.

### 4. Attach the custom domain

**a. Get the domain onto Cloudflare DNS**

- **.com and most other domains:** you can buy it directly in Cloudflare (**Domain Registration** → **Register Domains**), and DNS is set up automatically.
- **.co.il / .il domains:** Cloudflare can't register these. Buy it from an accredited Israeli registrar (listed on isoc.org.il), then:
  1. In Cloudflare: **Add a domain** → enter the domain → choose the **Free** plan.
  2. Cloudflare shows you two nameservers (like `xxx.ns.cloudflare.com`).
  3. In your registrar's control panel, replace the existing nameservers with those two.
  4. Wait until Cloudflare emails that the domain is **Active** (minutes to a day or two).

**b. Connect it to Pages**

1. Pages project → **Custom domains** → **Set up a custom domain**.
2. Enter `yourdomain.co.il` → **Continue** → **Activate domain**. Cloudflare creates the DNS record for you.
3. Repeat for `www.yourdomain.co.il`.
4. Pick one as the main address and redirect the other to it: **Rules** → **Redirect Rules** → create one from the "Redirect from WWW to root" template (or the reverse).
5. SSL is automatic. Under **SSL/TLS** → **Edge Certificates**, turn on **Always Use HTTPS**.

Then replace every `[[DOMAIN]]` in the HTML files, `robots.txt` and `sitemap.xml` with the real domain.

### 5. Optional: protect `main` so merges need approval

1. Repo → **Settings** → **Rules** → **Rulesets** → **New ruleset** → **New branch ruleset**.
2. Name: `Protect main`. Enforcement status: **Active**.
3. **Target branches** → **Add target** → **Include default branch**.
4. Tick:
   - **Restrict deletions**
   - **Block force pushes**
   - **Require a pull request before merging**, with **Required approvals: 1**
5. **Create**.

Note: on a *free* plan, rulesets and branch protection are only enforced on **public** repos. For a private repo you need a paid plan (**GitHub Pro** if the repo is under a personal account, **GitHub Team** if it's under an organization), or just agree with your partner to always go through pull requests. Without the paid plan, the safety net is that Cloudflare keeps every deployment, so you can roll back instantly from **Deployments** → **⋯** → **Rollback to this deployment**.

### 6. Email on the domain

Pick one:

**Option A: Cloudflare Email Routing (free, forwarding only)**

Good if you just want `info@yourdomain` to land in existing Gmail inboxes.

1. Cloudflare dashboard → your domain → **Email** → **Email Routing** → **Get started**.
2. Create an address (e.g. `info`) and set the destination to your personal email. Confirm the verification email.
3. Let Cloudflare add the MX and SPF records it proposes.
4. Replying *as* `info@yourdomain` takes extra setup (Gmail "Send mail as" through an SMTP service), so if you'll reply often, choose Option B.

**Option B: Google Workspace (paid, full mailboxes)**

Real inboxes that send and receive as `you@yourdomain`, plus calendar and Drive.

1. Sign up at https://workspace.google.com and enter your domain.
2. Google asks you to verify the domain with a TXT record: add it in Cloudflare → **DNS** → **Records** → **Add record**.
3. Add Google's MX record (Cloudflare → DNS → **Add record** → type `MX`, name `@`, mail server `smtp.google.com`, priority `1`). Remove any Email Routing MX records first, as the two can't coexist.
4. Add SPF: TXT record on `@` with `v=spf1 include:_spf.google.com ~all`.
5. In the Google Admin console, generate a **DKIM** key (Apps → Google Workspace → Gmail → Authenticate email) and add it as a TXT record in Cloudflare, then click **Start authentication**.
6. Add DMARC: TXT record named `_dmarc` with `v=DMARC1; p=none; rua=mailto:YOUR-EMAIL`. After a few weeks of clean reports, tighten to `p=quarantine`.

Setting SPF, DKIM and DMARC matters: without them, your emails are likely to land in customers' spam folders.

### 7. Before going live

- [ ] No `[[PLACEHOLDER]]` text left: `grep -rn "\[\[" --include=*.html --include=*.xml --include=*.txt .`
- [ ] `assets/img/og-image.png` (1200×630) and `assets/img/apple-touch-icon.png` (180×180) exist
- [ ] Privacy policy reviewed
- [ ] Test a shared link at https://www.opengraph.xyz
- [ ] Run Lighthouse (Chrome DevTools → Lighthouse) on the live site
