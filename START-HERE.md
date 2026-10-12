# Baba Aramdev: GitHub + Gemini setup

The site opens in kiosk mode by default. Hold to talk, ask, release, and Baba replies aloud. Add `?kiosk=0` to the site URL for the regular interface.

You will use **GitHub Pages for the screen** and **Cloudflare Workers for Gemini**. The backend keeps your Gemini key private. GitHub Pages alone cannot run this backend.

## 1. Put the screen on GitHub

1. Unzip the download. Open the `Baba-Aramdev-GitHub` folder.
2. On GitHub, create a public repository named `baba-aramdev`.
3. Use **Add file → Upload files**. Upload `index.html` and `config.js` to the repository root. Do not upload the ZIP itself or nest those files inside another folder. Commit the upload.
4. Go to **Settings → Pages**. Choose **Deploy from a branch**, select **main** and **/(root)**, then save.
5. Wait for deployment. The expected URL for your account is:

   `https://raghav95nanda.github.io/baba-aramdev/`

The screen works immediately with clearly labelled, prewritten demo fortunes. Uploading the other files is optional for hosting; the two frontend files are enough.

## 2. Get a Gemini API key

1. Open https://aistudio.google.com/apikey and sign in to your Google account.
2. Create a key in your selected Google project, or use its existing Gemini API key. If you have no project available, follow AI Studio's project-creation/import flow.
3. Keep the key ready for Cloudflare. **Never paste it into `index.html`, `config.js`, or your GitHub repository.**

The key belongs to your Google project. Usage follows that project's available models, quota and billing. Your ChatGPT subscription does not configure this API connection.

## 3. Put the backend on Cloudflare

1. Sign in or create an account at https://dash.cloudflare.com.
2. Open **Workers & Pages**, create a Worker, and deploy a starter Worker. Name it something like `baba-aramdev-api`.
3. Open its code editor. Open `backend/cloudflare-worker.js` from this download in a text editor, copy **all** its contents, and replace the starter code with it. Save and deploy.
4. Copy your Worker URL, for example:

   `https://baba-aramdev-api.your-account.workers.dev`

5. In the Worker, go to **Settings → Variables and Secrets → Add**. Add these values and deploy the changes:

| Name | Type | Value |
| --- | --- | --- |
| `GEMINI_API_KEY` | Secret | Your Google AI Studio API key |
| `KIOSK_ACCESS_CODE` | Secret | A unique password you create, at least 20 characters long |
| `ALLOWED_ORIGIN` | Text | `https://raghav95nanda.github.io` |

For `ALLOWED_ORIGIN`, use only the site origin: **no `/baba-aramdev/` path**. If your GitHub username or hosting domain differs, change the origin accordingly.

Your kiosk access code is separate from the Gemini API key. It lets your kiosk use the backend without putting the Gemini key in the screen. Keep the access code out of GitHub too. Someone who knows this code can use the backend, so treat it as private.

## 4. Connect Baba

1. Open your GitHub Pages site.
2. Tap the **gear** in the upper-right corner, then **Connect Gemini**.
3. Enter your Worker URL and the **KIOSK_ACCESS_CODE** you created. Do not enter your Gemini API key here.
4. Select **Test & connect**. It should say **Connected. Baba can now answer with Gemini.** Close the dialogs.
5. Hold the talk button, allow microphone access, ask a question, and release it. Or use **Type instead**.

The Worker URL and access code are kept for this browser tab's session. Reconnect if your browser starts a new session. Optionally put only the Worker URL in `config.js` to prefill it; never put either secret there.

## Troubleshooting

- **Access code not accepted:** enter the exact `KIOSK_ACCESS_CODE` from Cloudflare.
- **Could not reach the Worker:** check its URL, deployment and `ALLOWED_ORIGIN`.
- **Backend connected, Gemini not configured:** add the `GEMINI_API_KEY` secret and deploy the change.
- **Gemini connection needs attention:** check the key, its API restrictions, the selected model and the project's quota in AI Studio. Optional Worker text variable `GEMINI_MODEL` overrides the default `gemini-3.8-flash` with a compatible structured-output model available to your project.
- **Oracle busy:** Gemini may have hit a quota/rate limit. Wait and check the project's usage.
- **Microphone unavailable:** use a browser that supports Web Speech recognition, allow microphone permission, and use the live HTTPS site. Voice capture usually needs the internet. Text input remains available.
- **No spoken reply:** check device volume, the gear's spoken-reply switch, or tap **Hear that again**. Browser voice availability varies.

Baba offers general wellness guidance. Gemini replies can be mistaken; this is not a diagnostic or treatment tool. Questions are sent to Google when connected. The app does not save them, and kiosk screens clear between visitors.

## Optional command-line deployment

The included `backend/wrangler.jsonc` supports Wrangler. From the `backend` folder, run `npx wrangler deploy`, then `npx wrangler secret put GEMINI_API_KEY` and `npx wrangler secret put KIOSK_ACCESS_CODE`. Enter secret values at the prompts. Set `ALLOWED_ORIGIN` in the configuration first.

## Verification

Export checks passed for frontend syntax, UI bindings, local demo responses, backend authentication, origin/preflight behaviour, Gemini request structure and error handling. No live Gemini request was made because no key was supplied. Microphone, audio and visual browser testing remain unverified.

## Official references

- GitHub Pages: https://docs.github.com/en/pages/getting-started-with-github-pages/configuring-a-publishing-source-for-your-github-pages-site
- Gemini API keys: https://ai.google.dev/gemini-api/docs/api-key
- Cloudflare Worker dashboard: https://developers.cloudflare.com/workers/get-started/dashboard/
- Worker secrets: https://developers.cloudflare.com/workers/configuration/secrets/
- Gemini structured output: https://ai.google.dev/gemini-api/docs/generate-content/structured-output
