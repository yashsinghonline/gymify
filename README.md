# Gymify

A workout tracker that runs in the browser: plan a week, log sets, watch the numbers move.
Dark, flat, one lime accent.

It works as a plain static site. There is no server to run and no account to make.

## Your data

- The first time the app opens it asks eight things: name, age, sex, height, weight, goal,
  experience and training days per week. All of them can be changed later in
  **Settings → Account → Your profile**.
- The answers, and everything logged afterwards, are stored in that browser on that device
  (`localStorage`, under `gym_state_v1`). Nothing is sent anywhere: the first-run screen and the
  profile code make no network request, and a test (`frontend/src/lib/me.test.js`) keeps it so.
- The app starts empty. There is no sample data.
- Because the data lives in the browser, clearing the browser's site data deletes it, and a
  second device starts from zero. **Settings → Data & backup** exports a file that can be
  imported on another device.

## Run it

Needs Node 20 or newer.

```sh
bash run.sh              # builds, then serves http://localhost:4173
PORT=8080 bash run.sh
```

While working on it:

```sh
cd frontend
npm ci
VITE_DEMO=1 npm run dev  # live reload
npm test                 # on a small machine: npx vitest run --maxWorkers=2 --shard=1/6 … 6/6
```

`VITE_DEMO=1` is the switch for the device-only build described above
(`frontend/src/lib/demo.js`). Without it the app expects the server in `api/` and shows a
sign-in screen instead of the first-run questions.

## Put it online for free

**GitHub Pages**

1. Create a new, empty repository on GitHub. Do not use the Fork button on another project.
2. In this folder:
   ```sh
   git init -b main
   git add .
   git commit -m "Gymify"
   git remote add origin https://github.com/<you>/<repository>.git
   git push -u origin main
   ```
3. On GitHub: **Settings → Pages → Source: GitHub Actions**.

`.github/workflows/pages.yml` then builds and publishes on every push to `main`. The address is
`https://<you>.github.io/<repository>/`.

**Any other static host** (Netlify, Cloudflare Pages, a plain web server): build with
`VITE_DEMO=1 npm run build` in `frontend/` and upload the `frontend/dist` folder.

Two optional build settings:

| Variable | What it does |
|---|---|
| `VITE_SOURCE_URL` | Address of your repository. Adds a "Source code" link on the About page. The Pages workflow sets it for you. |
| `VITE_RELEASES_REPO` | `owner/name` of a GitHub repository whose releases the app should check for updates. Unset, the app never asks. |

## Exercise pictures

The 6,554 exercise pictures and animations are not stored in this folder (216 MB). To add them:

```sh
bash scripts/fetch-media.sh
```

That downloads `catalogue/media` and `catalogue/exercises` and leaves no git repository or
remote behind. Commit both folders to your own repository afterwards; `run.sh` and the Pages
workflow pick them up on their own. Without them the app works and shows exercises as text.

The pictures are not yours and not covered by the code's licence. Read the next section before
publishing a copy that includes them.

## Licence and credits

Gymify is a modified version of **openGym**, © 2026 Duarte Santos, licensed under the
**GNU Affero General Public License v3.0 or later** ([LICENSE](LICENSE)). Gymify is licensed
under the same terms. The changes were made in November 2026: the name, the visual design
([DESIGN.md](DESIGN.md)), the first-run profile, and the removal of sample data and of links to
the original project's services.

What that licence asks of anyone who publishes a copy:

- Keep `LICENSE`, `NOTICE.md` and the notice at the bottom of **Settings → About**.
- Make the source of your version available to the people who use it. A public repository and
  `VITE_SOURCE_URL` do that.

**The exercise media has its own terms.** The pictures and animations are © Aliaksandr
Makatserchyk, Gym visual (gymvisual.com). They were licensed for use in openGym only and are not
under the AGPL. `NOTICE.md` has the full text. In short:

- A non-commercial copy may keep the media unmodified, at its 180 px size, together with that
  notice and the credit line the app shows.
- Any commercial use, or any use that drops the notice, needs a licence of your own from
  gymvisual.com.
- The media may not be used to train or prompt AI models.

If you want a copy with no such notice at all, publish it without `catalogue/media` or with
pictures you have the rights to.

Other third-party work (fonts, icons, body diagram, QR code libraries) is listed in
[NOTICE.md](NOTICE.md).

## What is in this folder

| Path | What it is |
|---|---|
| `frontend/` | The app (React, Vite). This is what gets published. |
| `frontend/src/views/Welcome.jsx`, `frontend/src/lib/me.js` | The first-run questions and the profile behind them. |
| `frontend/src/lib/brand.js` | Name, palette and accent in one place. |
| `frontend/src/design.css`, `DESIGN.md` | The visual design and its reference. |
| `catalogue/`, `scripts/catalogue/` | Exercise data and the scripts that build it into the app. |
| `scripts/greyscale/serve.mjs` | The small static server `run.sh` uses. |
| `scripts/fetch-media.sh` | One-time download of the exercise media. |
| `.github/workflows/pages.yml` | Publishing to GitHub Pages. |

Also here, unchanged from the original project and still carrying its name inside: the optional
server (`api/`, `web/`, `mcp/`, `docker-compose.yml`, `helm/`, `kubernetes/`), its documentation
(`docs/`), the watch app (`watch-zepp/`), the Android and iOS shells (`frontend/android`,
`frontend/ios`) and the release scripts in `scripts/`. The published web app uses none of them.
They are kept so a server-backed or native build stays possible; they were not adapted or tested
for Gymify, and deleting them does not affect the site.
