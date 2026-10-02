# Backyard BBQ RSVP: setup (about 15 minutes)

## 1. Make the Sheet and backend
1. Create a blank Google Sheet (sheets.new) and name it something like **BBQ RSVPs**.
2. In the Sheet: **Extensions → Apps Script**. Delete the starter code, paste in all of `Code.gs`, and save.
3. In the function dropdown at the top, pick **setup** and click **Run**. Approve the permissions prompt.
   - Google will warn that the app is "unverified." That's normal for your own script: **Advanced → Go to (project name)**.
   - Your Sheet now has two tabs: **RSVPs** and **Details**.
4. On the **Details** tab, fill in `address` when you have it. The **Directions** button appears on the page only once an address is there.
   - `note` is optional and shows under the details (e.g. "Park on the street, gate's on the left").
   - `mapsUrl` is optional. Paste a Google Maps share link here to send people to an exact pin instead of the typed address.
   - Already ran `setup` before `mapsUrl` existed? Just type `mapsUrl` into column A of an empty row yourself.

## 2. Deploy the web app
1. In Apps Script: **Deploy → New deployment → gear icon → Web app**.
2. Set **Execute as: Me** and **Who has access: Anyone**.
3. Click **Deploy** and copy the **Web app URL** (it ends in `/exec`).
4. Test it by opening that URL in your browser. You should see something like `{"ok":true,"details":{...},"items":[]}`.

> If you edit `Code.gs` later: **Deploy → Manage deployments → pencil → Version: New version → Deploy**. This keeps the same URL. Choosing "New deployment" instead creates a new URL.

## 3. Hook up the page
In `index.html`, find this line near the bottom and paste your URL in:
```js
const ENDPOINT = 'PASTE_YOUR_APPS_SCRIPT_URL_HERE';
```
Until you do, the page runs in **demo mode** with sample items, so you can preview it safely.

## 4. Put it on GitHub Pages
1. Create a new repo (e.g. `bbq`) and upload `index.html` to the root. Don't upload `Code.gs`; it only lives in Google.
2. Go to **Settings → Pages → Source: Deploy from a branch → main / (root) → Save**.
3. After about a minute, the page is live at `https://<your-username>.github.io/bbq/`.

## 5. Test end to end
1. Submit an RSVP with a test name, and check that a row appears in **RSVPs** (and an email arrives).
2. Change that row's **Status** to `cancelled`, refresh the page, and confirm the item is gone.
3. Delete the test row.

## Printable invites (optional)
`invite-print.html` makes four invites per letter page. It works like the main page:
- **Address:** pulled from the same Sheet. Set `ENDPOINT` near the bottom of the file to the same `/exec` URL you used in `index.html`.
- **RSVP link and QR code:** worked out from the print page's own address, minus the file name. Upload it next to `index.html` (same folder in the repo) and it will point at your RSVP page automatically.
- **Opening it straight from your computer** (not the published page) can't work out the link. Fill in `RSVP_URL_OVERRIDE` in that case, or use the published page to print.
- Wait for the address to appear on the cards before you print.

## Managing RSVPs
- **Cancellation:** set Status to `cancelled`, or just delete the row.
- **Change what someone's bringing:** edit the Bringing cell.
- **Headcount:** in any empty cell, `=COUNTIFS(F:F,"active") + SUMIFS(C:C,F:F,"active")`.
- **Turn off the email notifications:** set `NOTIFY_ON_RSVP = false` in `Code.gs` and redeploy a new version.

## Notes
- Names never leave Google. The page only ever receives the item list and the Details tab.
- The address is stored in the Sheet rather than in the page, so it isn't sitting in your public GitHub repo.
- Calendar times assume **Eastern time** (1–7 PM EDT). If you're elsewhere, change `startUTC`/`endUTC` in `index.html`.