# Swastik Lifts

Powerbuilding coaching site for Swastik — one self-contained HTML file, no build step, no dependencies to install.

| File | What it is |
|---|---|
| `index.html` | The entire website. Photos and favicon are base64-embedded. |
| `backend.gs` | Google Apps Script that receives applications and lap times into a Google Sheet. |
| `DEPLOY.md` | Full deployment walkthrough. |

## Run it

Open `index.html` in a browser. That's it — what you see is what deploys.

## Put it online

GitHub Pages: push this repo, then **Settings → Pages → Deploy from a branch**, branch `main`, folder `/ (root)`. The site is served at `https://swastikkkkk.github.io/<repo-name>/`.

Netlify Drop and Cloudflare Pages work the same way by dragging `index.html` in.

## Configure

Open `index.html` and search for `const SL_CFG` near the top of the script — phone number, price, and the Apps Script endpoint all live in that one block. `DEPLOY.md` covers wiring up the backend so form submissions land in a spreadsheet.

## Notes

Deliberately one file. The only outside requests are Google Fonts plus three script tags on cdnjs (three.js r128, cannon.js 0.6.2, GSAP 3.12.5 with ScrollTrigger and Lenis). No npm, no bundler, no server.

The driving section degrades on its own: it checks for WebGL and `prefers-reduced-motion`, scales resolution, shadows and particle counts to whatever the device can hold, and is skipped entirely rather than shown broken if three.js fails to load.
