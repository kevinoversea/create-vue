# Cloudflare settings for medhub24.com

Written for a non-developer. There is one field that matters.

## The settings

Cloudflare → **Compute (Workers & Pages)** → the **medhub24** worker →
**Settings** → **Build**.

| Field | What to put | Why |
|---|---|---|
| **Branch** | **`main`** | The website is now on `main`. If Cloudflare was pointed at a branch that did not contain it, that alone would explain everything. |
| **Root directory** | **`deploy`** | **Must be `deploy`.** At `/` the build dies before it reaches your website — see below. |
| Build command | *empty, or leave as-is* | Either works now. The website is plain files; there is nothing to build. |
| Deploy command | `npx wrangler deploy` | The command that publishes it. |

Click **Save**, then find the most recent deployment and click
**Retry deployment**.

### Why "Root directory" must be `deploy`

A build log showed exactly what happens when it is `/`:

```
Initializing 3s ✓   Cloning 8s ✓   Installing 3s ✗   Building —   Deploying —
```

It fails at **Installing**, in three seconds, and never reaches your website at
all.

The reason is nothing to do with your site. It sits inside a copy of an
unrelated tool for Vue developers, and that tool's dependency list is from 2022
— in a format today's tools refuse to read. It also refers to a second project
that is not downloaded. Cloudflare tries to set all of that up first, and stops.

`deploy` is a small folder holding only the publishing instructions. Nothing to
install there, so nothing to fail. Your website is published from it.

The build command can stay exactly as it is — that folder now answers it with a
harmless "nothing to build".

---

## If the site still does not appear

**Do this one thing first.** It is worth more than any amount of guessing.

On the worker's main page in Cloudflare there is a link ending in
**`.workers.dev`**. Open it.

| What you see | What it means | What is left |
|---|---|---|
| The website loads | Publishing works. | Only connecting your domain. Small fix. |
| An error, or nothing | Publishing is still failing. | The build log says why — see below. |

Those two need completely different fixes, which is why this click is the
fastest way forward.

### Getting the build log

Cloudflare → the worker → **Deployments** (or **Builds**) → click the most
recent one → scroll to the bottom → copy the red text. A screenshot is fine.

---

## About the two web addresses

Only **www.medhub24.com** is connected to the website. The bare
**medhub24.com** reaches it by redirecting to the www address.

They are kept separate on purpose. Publishing is a single all-or-nothing
operation: if connecting one address fails, the whole publish fails and
*nothing* goes live — including the address that would have worked. The bare
domain is the more likely of the two to have a conflict, and it is not your
main address, so it is handled by a redirect instead. A problem there can no
longer stop your website from publishing.

---

## What "working" looks like

When it is right, `https://www.medhub24.com/` should:

- open in **Khmer**, not English
- show a padlock in the address bar
- show the founder's photograph, not a green circle with initials
- switch to English when you tap **KH / EN**, and stay English if you reload
- show **MedHub24.com** in the footer (not MedHub26)
- open your dialler on **+855 12 464 639** when you tap the phone number
- have nothing running off the right edge of a phone screen

---

## Security

Any API token pasted into a chat should be revoked:
**My Profile → API Tokens →** the three dots beside the token **→ Delete**.
Creating a fresh one takes a minute; a leaked one can redirect your domain.
