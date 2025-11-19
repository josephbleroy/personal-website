# Minimal GRC Engineering Landing Page Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Transform the GRC Engineering landing page into a minimal index that automatically lists all GRC Engineering posts with title, date, and series.

**Architecture:** Create a custom Hugo layout template for the GRC Engineering page that queries posts by category and displays them chronologically. Update the page content to be minimal (2-3 sentence intro).

**Tech Stack:** Hugo static site generator, hello-friend-ng theme, Hugo templating language

---

## Task 1: Create Custom Hugo Layout Template

**Files:**
- Create: `layouts/_default/grc-engineering.html`

**Step 1: Create layouts directory structure**

```bash
mkdir -p layouts/_default
```

Expected: Directory created successfully

**Step 2: Create the custom layout template**

Create `layouts/_default/grc-engineering.html` with the following content:

```html
{{ define "main" }}
    <div class="content">
        <main class="posts">
            <h1>{{ .Title }}</h1>

            {{ if .Content }}
                <div class="content">{{ .Content }}</div>
            {{ end }}

            {{ $grcPosts := where .Site.RegularPages "Params.categories" "intersect" (slice "GRC Engineering") }}
            {{ $grcPosts = $grcPosts.ByDate.Reverse }}

            {{ if $grcPosts }}
                <div class="posts-group">
                    <ul class="posts-list">
                        {{ range $grcPosts }}
                            <li class="post-item">
                                <a href="{{.Permalink}}" class="post-item-inner">
                                    <span class="post-title">{{.Title}}</span>
                                    <span class="post-meta">
                                        <span class="post-day">
                                            {{ if .Site.Params.dateformShort }}
                                                {{ time.Format .Site.Params.dateformShort .Date }}
                                            {{ else }}
                                                {{ time.Format "Jan 2" .Date }}
                                            {{ end }}
                                        </span>
                                        {{ with .Params.series }}
                                            {{ if reflect.IsSlice . }}
                                                {{ range . }}
                                                    <span class="post-series"> • {{ . }}</span>
                                                {{ end }}
                                            {{ else }}
                                                <span class="post-series"> • {{ . }}</span>
                                            {{ end }}
                                        {{ end }}
                                    </span>
                                </a>
                            </li>
                        {{ end }}
                    </ul>
                </div>
            {{ else }}
                <p><em>No posts yet. Check back soon!</em></p>
            {{ end }}
        </main>
    </div>
{{ end }}
```

**Step 3: Verify the file was created**

```bash
ls -la layouts/_default/grc-engineering.html
```

Expected: File exists

**Step 4: Commit the layout template**

```bash
git add layouts/_default/grc-engineering.html
git commit -m "feat: add custom layout for GRC Engineering landing page

Create Hugo template that automatically lists posts by category with
title, date, and series badge."
```

Expected: Commit successful

---

## Task 2: Update GRC Engineering Page Content

**Files:**
- Modify: `content/grc-engineering.md`

**Step 1: Read current content**

```bash
cat content/grc-engineering.md
```

Expected: See current heavy content

**Step 2: Replace with minimal content**

Update `content/grc-engineering.md` to:

```markdown
---
title: "GRC Engineering"
date: 2025-11-18
type: page
layout: grc-engineering
---

Automating AWS compliance controls that reduce audit friction and improve security. This is my learning journey documenting practical implementations with working Infrastructure-as-Code.
```

**Step 3: Verify the changes**

```bash
cat content/grc-engineering.md
```

Expected: See minimal content with `layout: grc-engineering` in frontmatter

**Step 4: Commit the updated content**

```bash
git add content/grc-engineering.md
git commit -m "refactor: simplify GRC Engineering landing page

Reduce to minimal intro; posts will be listed automatically by
custom layout template."
```

Expected: Commit successful

---

## Task 3: Test the Landing Page Locally

**Files:**
- Test: Local Hugo development server

**Step 1: Start Hugo development server**

```bash
hugo server -D
```

Expected: Server starts successfully, shows URL (typically http://localhost:1313)

**Step 2: Verify the GRC Engineering page renders**

Open browser to: `http://localhost:1313/grc-engineering/`

Expected output:
- Page title "GRC Engineering" displays
- Brief intro paragraph displays
- If no posts exist yet: "No posts yet. Check back soon!" message
- If posts exist: Posts listed chronologically (newest first) with title, date, and series

**Step 3: Stop the Hugo server**

Press `Ctrl+C` in the terminal

Expected: Server stops

---

## Task 4: Create Test Post (Optional - for validation)

**Files:**
- Create: `content/posts/test-grc-post.md` (temporary test file)

**Step 1: Create a test GRC Engineering post**

```markdown
---
title: "AWS Organizations & Account Structure"
date: 2025-11-18
categories: ["GRC Engineering"]
series: ["AWS Foundations for Compliance"]
tags: ["AWS", "OpenTofu", "Compliance"]
---

This is a test post for the GRC Engineering section.
```

**Step 2: Start Hugo server and verify**

```bash
hugo server -D
```

Open: `http://localhost:1313/grc-engineering/`

Expected:
- Test post appears in the list
- Shows: "AWS Organizations & Account Structure"
- Shows: Date "Nov 18"
- Shows: Series "AWS Foundations for Compliance"

**Step 3: Stop server and remove test post**

```bash
# Stop server with Ctrl+C
rm content/posts/test-grc-post.md
```

Expected: Test file removed (only if you don't want to keep it)

---

## Task 5: Update Original GRC Engineering Design Doc

**Files:**
- Modify: `docs/plans/2025-11-18-grc-engineering-content-design.md`

**Step 1: Add implementation note to original design doc**

Add to the top of the "Site Organization (Hugo)" section:

```markdown
**Implementation Note (2025-11-18)**: The landing page has been simplified to minimal content with automatic post listing. See `docs/plans/2025-11-18-minimal-grc-landing-page-design.md` and `docs/plans/2025-11-18-minimal-grc-landing-page-implementation.md` for details.
```

**Step 2: Commit the update**

```bash
git add docs/plans/2025-11-18-grc-engineering-content-design.md
git commit -m "docs: add implementation note to original GRC design

Reference new minimal landing page design and implementation."
```

Expected: Commit successful

---

## Verification Checklist

After completing all tasks, verify:

- [ ] Custom layout template exists at `layouts/_default/grc-engineering.html`
- [ ] GRC Engineering page content is minimal (2-3 sentences)
- [ ] Page frontmatter includes `layout: grc-engineering`
- [ ] Hugo builds without errors (`hugo` command)
- [ ] Landing page displays correctly in browser
- [ ] Post listing works when posts with `categories: ["GRC Engineering"]` exist
- [ ] Series badge displays correctly
- [ ] All changes committed to git

---

## Post Requirements Documentation

For future reference, each GRC Engineering post must include this frontmatter:

```yaml
---
title: "Post Title"
date: 2025-11-18
categories: ["GRC Engineering"]
series: ["Series Name"]  # e.g., "AWS Foundations for Compliance"
tags: ["AWS", "OpenTofu", "Compliance", ...]
---
```

The landing page will automatically pick up any post with `categories` containing "GRC Engineering".
