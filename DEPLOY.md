# Swastik Lifts — going live

Three files in this folder:

| File | What it is |
|---|---|
| `index.html` | The entire website. One file, no build step, photos already embedded. |
| `backend.gs` | Google Apps Script that receives applications and lap times into a Google Sheet. |
| `DEPLOY.md` | This page. |

You can put the site online first and wire the backend up afterwards. Nothing breaks in the meantime: until an endpoint is set, an application is saved on the visitor's device and handed to you over WhatsApp with every answer pre-filled, and lap times are kept per-device instead of on a shared board.

## 1. Put the site online (about 3 minutes)

GitHub Pages, free, no account changes needed beyond a repo:

1. Create a new public repository, for example `swastik-lifts`.
2. Upload `index.html` to the root of it. The file must be named exactly `index.html`.
3. Repo **Settings → Pages**. Under *Build and deployment*, set Source to **Deploy from a branch**, branch `main`, folder `/ (root)`. Save.
4. Wait a minute, then open `https://<your-username>.github.io/swastik-lifts/`.

Netlify Drop (`app.netlify.com/drop`) and Cloudflare Pages both work the same way if you'd rather drag the file in.

## 2. Change the phone number and price

Open `index.html` and search for `const SL_CFG`. It sits near the top of the script and is the only block you need to touch:

```js
const SL_CFG={
  SCRIPT_URL:"PASTE_YOUR_APPS_SCRIPT_URL_HERE",
  SECRET_KEY:"PASTE_YOUR_SECRET_KEY_HERE",
  WHATSAPP:"917384221979",   // country code + number, digits only
  EMAIL:"",                  // optional email fallback on the thank-you screen
  PRICE:"₹1,980"
};
```

`WHATSAPP` drives every WhatsApp link on the page, including the floating button and the in-game gate, so changing it here changes all of them.

## 3. Where the form data goes

Right now: nowhere on a server, because `SCRIPT_URL` is still a placeholder. The form still works and still reaches you, through the WhatsApp handoff on the final screen. To get applications landing in a spreadsheet instead:

1. Go to `sheets.new`, name the spreadsheet **Swastik Lifts**.
2. **Extensions → Apps Script**. Delete the sample code, paste in all of `backend.gs`.
3. At the top of that file, change `SECRET_KEY` to any random string of your own. Put your email in `NOTIFY_EMAIL` if you want a mail each time someone applies.
4. **Deploy → New deployment → Web app**, with *Execute as* **Me** and *Who has access* **Anyone**. Deploy, approve the Google permission prompt, copy the `/exec` URL it gives you.
5. Back in `index.html`, set `SCRIPT_URL` to that URL and `SECRET_KEY` to the same string you chose in step 3.
6. Re-upload `index.html`.

Test it by submitting the form once. A row appears in the **Applications** tab, with one column per question. New questions added to the form later get their own column automatically; nothing is dropped.

Two things worth knowing. First, `Who has access: Anyone` is what lets a browser POST to it, and the shared `SECRET_KEY` is what stops anyone else writing rows. Second, after any edit to `backend.gs` you have to do **Deploy → Manage deployments → edit → Version: New version**, otherwise the live URL keeps serving the old code.

There is a `selfTest()` function at the bottom of `backend.gs`. Run it once from the Apps Script editor to confirm the sheet wiring works before you touch the website.

## 4. The lap-time board

The road in the driving section is a closed circuit with a start/finish gantry, four sector checkpoints and cut-detection. Press **L** (or *Time a lap*) to arm the timer, cross the line, and the lap is timed; **B** opens the board.

With no backend, each visitor sees their own times. Once `SCRIPT_URL` is set, saved laps post to the **Lap times** tab and the board shows everyone's, best lap per name, fastest first. The backend rejects anything under 25 seconds or over 10 minutes, so a tampered time can't top the board.

## 5. Before you call it done

- Swap the placeholder squat and bench photos for the real PR shots if you have them.
- The three testimonials are written as examples. Replace the names and quotes with real ones, or cut that block.
- `EMAIL` in CONFIG is blank, so the thank-you screen only offers WhatsApp. Add an address if you want both.
- If you want the site on a custom domain, add a `CNAME` file next to `index.html` containing just the domain, then point a CNAME record at `<username>.github.io`.

## Notes for whoever maintains it

It is deliberately one file. Photos and the favicon are base64-embedded, and the only outside requests are Google Fonts plus three script tags on cdnjs (three.js r128, cannon.js 0.6.2, GSAP 3.12.5 with ScrollTrigger and Lenis). No npm, no bundler, no server. Open `index.html` in a browser and what you see is what deploys.

The driving section degrades on its own: it checks for WebGL and for `prefers-reduced-motion`, drops shadows, particle counts and the grass layer on low-power devices, and the whole game is skipped rather than shown broken if three.js fails to load.
