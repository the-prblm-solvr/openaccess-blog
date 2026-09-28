# openaccess.design — blog content

This repository is the source of truth for the blog on **https://openaccess.design**.

Editing `posts.json` here publishes to the live site. A cron job on the Hostinger
account pulls this file across every hour, writes it into the site's web root and
regenerates `sitemap.xml`. Nothing needs to be rebuilt or redeployed.

The website itself never reads from GitHub at runtime — it serves its own copy. If
this repo or GitHub is unreachable, the site keeps serving the last post list it
successfully pulled.

## Publishing a post

1. Add the post's image to `blog/` (see the naming rule below).
2. Add a new object to the **front** of the array in `posts.json` — the array is
   ordered newest first, and both the blog listing and the homepage rely on that.
3. Commit. The post is live within the hour.

## Post format

```jsonc
{
  "slug": "a-url-safe-title",        // becomes https://openaccess.design/a-url-safe-title
  "title": "A URL Safe Title",
  "description": "One or two sentences. Used on the listing cards and as the page summary.",
  "date": "M/D/YYYY",                 // e.g. "3/14/2026" — no leading zeros
  "readTime": "5 min read",
  "tags": [],
  "image": "/blog/your-image.jpg",    // must start with /blog/
  "imageAlt": "What the image shows, for screen readers. Never leave this empty.",
  "content": [
    { "type": "h2", "text": "A section heading" },
    { "type": "h3", "text": "A sub-heading" },
    { "type": "p",  "text": "A paragraph." },
    { "type": "ul", "items": ["A bullet", "Another bullet"] },
    { "type": "ol", "items": ["A numbered point", "Another"] }
  ]
}
```

`h2`, `h3`, `p`, `ul` and `ol` are the only block types the site can render.
Anything else is ignored, so a post using one will have gaps in it.

## Rules that matter

**Every image needs real alt text.** This is an accessibility agency's own site —
`imageAlt` describes what the image shows, and is the one field never to leave blank
or fill with the filename.

**Image names must be lowercase, hyphenated, and describe the subject** —
`blog-white-cane-beside-suv.jpg`, not `IMG_4021.jpg`. The name is the accessibility
convention used across the whole site.

**Don't reuse a slug.** Slugs are permanent URLs; changing one breaks every existing
link to that post, including from search results.

**Headings must not skip levels.** An `h3` belongs under an `h2`, never directly
after the title.

**Check claims before publishing.** The existing posts make specific legal and
standards claims (WCAG criteria, California AB-434). A confidently wrong post on an
accessibility consultancy's blog is expensive to walk back.

## Checking your work

`posts.json` must stay valid JSON — a trailing comma or a smart quote breaks the
blog for every visitor until it's fixed. Before committing:

```bash
python3 -m json.tool posts.json > /dev/null && echo "valid"
```

The site reads the file directly, so a syntax error takes the whole blog down, not
just the new post.
