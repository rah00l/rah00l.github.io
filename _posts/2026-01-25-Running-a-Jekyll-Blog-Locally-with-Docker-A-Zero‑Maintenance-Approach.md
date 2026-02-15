---
title: Running a Jekyll Blog Locally with Docker - A Zero‑Maintenance Approach
tags: [Jekyll, Ruby, Github-pages]
style: border
color: success
description: Running a Jekyll blog locally often means dealing with Ruby, Bundler, and dependency mismatches. This post explains why I chose Docker for a zero‑maintenance local setup, the issues I faced along the way, and how I solved them to get a reliable, one‑command development workflow.
---

## 1. The Real Problem Statement (The Why)

I maintain a **Jekyll-based blog / GitHub Pages site**.  
From time to time, I want to:

*   run it locally,
*   preview changes,
*   experiment with layouts, themes, and content,
*   and do all of this **without breaking my system or spending time re-fixing my environment**.

The key challenge wasn’t *how to run Jekyll* — it was:

> **How do I run this project on *any* machine, today or in the future, with minimal setup and zero dependency pain?**

***

<br/>

<img src="{{ site.baseurl }}/public/images/Running-a-Jekyll-Blog-Locally-with-Docker-A-Zero‑Maintenance-Approach.jpeg" alt="The Complete Code Review Checklist">  
<br/>

## 2. The Two Obvious Choices

When running a Jekyll site locally, there are two standard approaches.

### Option A — Native setup (install everything locally)

This means:

*   Install Ruby
*   Install the correct Bundler version
*   Install native dependencies (nokogiri, ffi, etc.)
*   Run:
    ```bash
    bundle install
    bundle exec jekyll serve
    ```

✅ **Pros**

*   Fast startup
*   Easy debugging with system tools

❌ **Cons**

*   Ruby versions differ across machines
*   Bundler version must match `Gemfile.lock`
*   macOS system Ruby vs Homebrew Ruby conflicts
*   High maintenance when switching machines
*   “Works on my machine” problems

I had already seen this firsthand:  
Bundler version mismatches (`Gemfile.lock` requiring 2.1.4 while the host had a newer one) caused immediate friction.

***

### Option B — Docker-based setup (containerized)

This means:

*   Do **not** install Ruby or Bundler on the host
*   Use Docker + Docker Compose
*   Encapsulate Ruby, Bundler, Jekyll, and gems inside a container

✅ **Pros**

*   Same environment everywhere
*   No local dependency pollution
*   Easy to onboard on a new machine
*   Reproducible and deterministic

❌ **Cons**

*   Slightly more setup initially
*   Some debugging tools missing inside minimal images
*   Need to understand container quirks (ports, volumes, platforms)

***

## 3. The Decision

I chose **Docker (Option B)**.

The reasoning was simple:

> I don’t want to “install and fix Ruby” every time I change machines.  
> I want a setup where I can clone the repo and run **one command**.

This aligned with my goal of a **zero‑maintenance local development environment**.

***

## 4. Docker Strategy: Two Variants

Even within Docker, there are two approaches.

### Docker Option A — “Zero‑maintenance” runtime container

*   Use the official `jekyll/jekyll` image
*   Mount the project directory
*   Run `bundle exec jekyll serve`

This is what we initially attempted.

### Docker Option B — Custom-built image

*   Write a `Dockerfile`
*   Pre-install Ruby, Bundler, and gems
*   Build a custom image

I intentionally chose **Docker Option A** first:

*   less code,
*   less maintenance,
*   faster to start.

The idea was: *“Use what GitHub Pages already provides.”*

***

## 5. Where Things Started to Break (And Why)

### Symptom #1: “The server is running, but the browser shows nothing”

*   Docker showed:
        0.0.0.0:4000 -> 4000/tcp
*   `nc -vz localhost 4000` succeeded ✅
*   But:
    ```bash
    curl http://localhost:4000
    ```
    returned:
        connection reset by peer

✅ **Key Insight**  
This was **not a Docker networking issue**.

The port was open, but the **application was crashing while handling requests**.

***

## 6. The Hidden Culprit: GitHub API Calls at Render Time

Digging into logs revealed errors like:

    Liquid Exception:
    GET https://api.github.com/repos/.../releases
    429 – abuse detection mechanism

### What was happening?

*   The site uses `github-pages` / `jekyll-github-metadata`
*   Some templates fetch GitHub data (repos, releases, metadata)
*   These calls happen **during page render**
*   GitHub blocks excessive unauthenticated requests
*   Jekyll crashes → connection resets → browser sees nothing

✅ **Important realization**  
Even though the server *starts*, **every HTTP request triggers a render**, and that render was failing.

***

## 7. Why This Was Confusing

From the outside:

*   Docker looked healthy
*   Port mapping was correct
*   TCP connections succeeded

But HTTP failed because:

> **Jekyll died while rendering the page**

This is a classic case where:

*   infrastructure is fine,
*   application logic is broken.

***

## 8. Fixing the GitHub API Problem

We had two valid solutions.

### Solution 1 — Authenticate GitHub API calls (chosen)

*   Create a GitHub Personal Access Token
*   Pass it into Docker via `.env`
*   Let `octokit` use authenticated requests (5000/hour)

This preserved all GitHub-powered features.

✅ We verified success by checking:

```ruby
Octokit::Client.new.rate_limit
# limit: 5000, remaining: 4xxx
```

***

### Solution 2 — Disable GitHub metadata in local dev

For pure content work, GitHub data isn’t always needed.

*   Create `_config.local.yml`
*   Exclude `jekyll-github-metadata`
*   Use:
    ```bash
    --config _config.yml,_config.local.yml
    ```

This makes local dev **immune** to GitHub API issues.

***

## 9. More Issues We Had to Solve (Docker-Specific)

### Apple Silicon (ARM) vs Image Architecture

The official `jekyll/jekyll:4` image does **not** provide an ARM build.

Result:

    image does not provide the specified platform (linux/arm64)

✅ **Fix**
Run the image under emulation:

```yaml
platform: linux/amd64
```

***

### Bundler / Gems Missing at Runtime

At one point:

    Bundler::GemNotFound

Why?

*   Containers are ephemeral
*   Gems were installed in a different run
*   Nothing persisted

✅ **Fix**

*   Always run `bundle install` at startup
*   Cache gems using a Docker volume

***

## 10. The Final, Repeatable Outcome

What we ended up with:

*   ✅ No Ruby or Bundler on the host
*   ✅ One‑command startup on any machine
*   ✅ GitHub API limits handled properly
*   ✅ Works on Apple Silicon
*   ✅ Deterministic and reproducible

This fully satisfies the original goal:

> **Run my blog locally on any machine without dependency headaches.**

***

## 11. Lessons Learned (The Takeaways)

1.  **“Port is open” ≠ “Application is healthy”**
2.  Jekyll can crash *during render*, not just at startup
3.  GitHub Pages plugins can cause runtime API calls
4.  Docker simplifies environment management, but:
    *   platform mismatches matter,
    *   volumes matter,
    *   startup commands matter
5.  Local dev ≠ production — configs should reflect that

***

## 12. When I Do This Again (My Personal Playbook)

*   Choose Docker if long-term maintenance matters
*   Authenticate any external APIs used during render
*   Separate **local** and **production** config concerns
*   Always verify:
    ```bash
    curl http://localhost:4000
    ```
    not just port mappings
*   Treat Docker logs as first-class debugging signals

***

### Closing Thought

This wasn’t really about Docker or Jekyll.

It was about **designing a development workflow that scales with time and machines**, not just one laptop.

Docker (Option A: minimalist runtime container) turned out to be the right choice — once the hidden interactions (GitHub APIs, Bundler state, platform differences) were understood and handled deliberately.
