# Deploying The Mare Girl Blog 🐴✿

This is a fully static site (one HTML file + a few assets) — easiest host is **Vercel**, free forever for a hobby site like this.

> **Quick note on "GitHub Pages → Vercel":** these are two *separate* hosts. You pick **one**. The recommended path below uses GitHub as the *source of truth* (where your files live) and Vercel as the *host* (what serves the live site). Every time you `git push`, Vercel re-deploys automatically.

---

## 1. Put the site on GitHub

If you've never used GitHub before, install [GitHub Desktop](https://desktop.github.com) — it's a friendly visual app for this.

1. Make a new GitHub account at [github.com](https://github.com) (free).
2. In GitHub Desktop → **File → New Repository** → name it `mare-girl-blog` → choose this project folder as the local path.
3. Click **Publish repository** (uncheck "keep this code private" if you want it open-source, or leave it checked — Vercel works either way).

You should now see your files at `github.com/YOUR_USERNAME/mare-girl-blog`.

---

## 2. Deploy to Vercel

1. Go to [vercel.com](https://vercel.com) → **Sign up with GitHub**.
2. Click **Add New → Project** → pick `mare-girl-blog` from the list.
3. **Framework Preset** = `Other` (no framework — this is plain HTML).
4. **Root Directory** = leave as `./`
5. Click **Deploy**.

~30 seconds later you'll have a live URL like `mare-girl-blog.vercel.app`. 🎉

From now on, any time you push a change to GitHub, Vercel auto-deploys it. No more manual uploads.

### Custom domain (optional)

Vercel dashboard → your project → **Settings → Domains** → add a domain you own (or buy one through Vercel for ~$15/year).

---

## 3. Turn on real visitor analytics

In your Vercel project dashboard:

1. Click the **Analytics** tab.
2. Click **Enable Web Analytics**.

That's it. The tracking script is already in `index.html` (it does nothing on localhost, starts collecting once you're on a `.vercel.app` URL).

You'll see:
- Pageviews per day
- Top pages
- Top referrers (where visitors came from)
- Countries
- **No cookies** — no GDPR banner needed.

Free tier = 2,500 events/month, more than enough to start.

---

## 4. Auto-update sub count + new video list

The site can pull your live sub count and latest uploads straight from YouTube — no manual updates needed.

### Get a YouTube Data API key (free, 2 min)

1. Go to [Google Cloud Console → Credentials](https://console.cloud.google.com/apis/credentials).
2. Create a new project (e.g. `mare-girl-blog`).
3. **Enable APIs and Services** → search "YouTube Data API v3" → **Enable**.
4. Back on Credentials → **Create Credentials → API key**. Copy it.
5. (Important!) Click the key to edit it → under **API restrictions** select "YouTube Data API v3"; under **Application restrictions** pick "HTTP referrers" and add your domain(s): `mare-girl-blog.vercel.app/*`, and `*.vercel.app/*` for previews, and `localhost:*` if you want to test locally. This stops anyone from stealing your key.

### Paste it into the site

Open `index.html` and find this block near the top:

```html
<script>
  window.MAREGIRL_CONFIG = {
    YT_API_KEY: "",            // <-- paste key here
    YT_HANDLE:  "MareGirl_sehr"
  };
</script>
```

Paste the key between the quotes, commit, push. Vercel re-deploys. Sub count and the videos page now update themselves every time someone loads the site (cached for 1 hour locally to save API quota).

Free quota = 10,000 units/day. This site uses about 2 units per visitor. You'd need ~5,000 visitors a day before you'd hit the cap.

---

## 5. Adding new posts going forward

Two ways:

**Option A — Visit `?admin=1`** (quickest)
Open `your-site.com/?admin=1`, set a passcode, then click **+ New post** to write/publish posts inline. These save to your browser only (`localStorage`), so they don't sync across devices. Best for drafts/personal blog feel.

**Option B — Edit the HTML and push** (permanent)
Add an entry to the `BLOG_POSTS` array in `index.html`, commit, push. Vercel auto-deploys in ~30s. This is what shows up for everyone.

---

## 6. Adding new videos going forward

If you set up the YouTube API key (step 4), **you don't have to do anything** — uploads appear automatically within ~1 hour of going live on YouTube.

If you skipped the API key, add an entry to the `VIDEOS` array at the top:

```js
const VIDEOS = [
  { id: "v0", ytId: "NEW_VIDEO_ID", title: "New video!", days: 0, tag: "Star Equestrian", real: true },
  // ...
];
```

The video ID is the part after `youtu.be/` or `?v=` in any YouTube URL.

---

## Troubleshooting

- **Site loads but no analytics numbers** → analytics needs 24–48h of traffic before the dashboard fills in. Open your live URL a few times.
- **Sub count not updating** → check browser console for `[YouTube]` warnings. Most common cause: API key restrictions don't include your live domain.
- **`/_vercel/insights/script.js` 404 in console on localhost** → expected. The path only resolves on Vercel.
