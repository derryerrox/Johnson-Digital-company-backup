# JOHNSON DIGITAL

A responsive static website for JOHNSON DIGITAL, built with plain HTML, CSS and JavaScript. Supabase Edge Functions provide the optional server-side contact and media-management API.

## Run locally

Open `index.html` in a browser or serve this folder with any static web server. Google Fonts, Turnstile, Unsplash and the Supabase image catalogue require an internet connection. No npm packages are required by the website or Edge Function source.

## Enable email enquiries

The enquiry form already has a server-side delivery function. It sends submissions to `johnsondigitalco@gmail.com` using Resend, but it does not send anything until the services below are configured and deployed. No email credentials belong in the website source.

1. Create a [Supabase project](https://supabase.com/dashboard). Copy its project reference from the project URL.
2. Create a [Resend account](https://resend.com/) and verify a sender domain you control. Add the DNS records Resend provides, then create an API key. `EMAIL_FROM` must use an address on that verified domain; enquiries will be delivered to `johnsondigitalco@gmail.com`.
3. Create a [Cloudflare Turnstile widget](https://dash.cloudflare.com/?to=/:account/turnstile) for `derryerrox.github.io`. Copy its site key and secret key.
4. Install the [Supabase CLI](https://supabase.com/docs/guides/cli/getting-started#installing-the-supabase-cli), open PowerShell in this project folder, and run:

   ```powershell
   supabase login
   supabase link --project-ref YOUR-PROJECT-REF
   supabase db push
   ```

5. Copy `.env.example` to `.env.local` and replace the example values with your Resend API key, verified `EMAIL_FROM`, Turnstile secret, a long random `RATE_LIMIT_SECRET`, and `ADMIN_EMAILS` (the email address of the Supabase Auth administrator). Set `APP_ORIGIN=https://derryerrox.github.io`. Keep `.env.local` private; it is ignored by Git. Do not put secrets in `config.js`, commit them, or send them in chat.
6. Upload those server-only values to Supabase and deploy the email function:

   ```powershell
   supabase secrets set --env-file .env.local
   supabase functions deploy jd-portal
   ```

7. In `config.js`, set the **public** values (these are safe to publish):

   ```js
   formEndpoint: "https://YOUR-PROJECT-REF.supabase.co/functions/v1/jd-portal",
   turnstileSiteKey: "YOUR_PUBLIC_TURNSTILE_SITE_KEY",
   ```

   `mediaAdminUrl` can use the same function URL. Keep `siteUrl` set to the full GitHub Pages URL, including `/johnson-digital-1/`. `APP_ORIGIN` and the Turnstile widget use only the origin/hostname (`https://derryerrox.github.io`), not that project path.
8. Publish the updated website over HTTPS. Test the live form with a valid enquiry and CAPTCHA, then confirm the email arrives at `johnsondigitalco@gmail.com`.

Supabase automatically provides `SUPABASE_URL`, `SUPABASE_ANON_KEY` and `SUPABASE_SERVICE_ROLE_KEY` to hosted Edge Functions. Do not copy or override those project credentials in `.env.local`. The Turnstile site key is public; its secret key belongs only in `.env.local` and Supabase Function secrets.

To configure the separate protected media admin, create a Supabase Auth administrator, include that address in `ADMIN_EMAILS`, and set `mediaAdminUrl` to the same function URL. `socialLinks` can also be populated with official HTTPS profile URLs; unconfigured links stay hidden.

The function validates input server-side, requires consent and a server-verified Turnstile token, checks a hidden honeypot, rate-limits contact submissions and admin sign-in using an atomic database function, keeps administrator sessions in Secure/HttpOnly/SameSite cookies, checks an explicit email allowlist, validates image MIME signatures and file sizes, and restricts media mutations to authenticated administrators. The image bucket is intentionally public-read for published website images and private-write through the function. Direct anonymous/authenticated writes are not granted.

The image library initially contains the current site's image slots. Upload/replacing an image using one of these keys updates that image on the public site; new keys appear in the library but must also be added to a page section before they are displayed. Image uploads are resized/compressed to WebP in the browser when supported, with original format fallback.

No Supabase, Resend or Turnstile account has been connected or deployed from this local workspace. The privacy page is a pre-launch notice, not legal advice or a complete privacy policy. Confirm processing, retention, service-provider, rights and jurisdiction-specific details before collecting enquiries. Replace the demonstration portfolio concepts with approved agency work before presenting them as client projects.
