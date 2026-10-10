# My Event App: put it online

You need three free accounts: Supabase (database and sign-in), Google Cloud (only for the "Continue with Google" button), and Netlify (hosting). Allow about 30 minutes. A computer is much easier than a phone for this.

## 1. Create the database (Supabase)

1. Go to supabase.com, sign up, and click **New project**. Pick any name and a strong database password. Wait until it finishes setting up.
2. Open **SQL Editor**, click **New query**, paste the whole of `supabase/schema.sql`, and click **Run**. It should finish with "Success".
3. Open **Project Settings > API**. Copy the **Project URL** and the **anon public** key.
4. Open `config.js` in this folder and paste both values in place of `YOUR_SUPABASE_URL` and `YOUR_SUPABASE_ANON_KEY`. Save.

The anon key is meant to be public. The rules in `schema.sql` decide who can read and change data.

## 2. Put the app online (Netlify)

1. Go to app.netlify.com/drop and sign in.
2. Drag this whole folder onto the page. You get a link like `https://random-name.netlify.app`.
3. Under **Site configuration > Change site name**, pick a nicer name. Your link is now `https://yourname.netlify.app`.

## 3. Tell Supabase your link

In Supabase open **Authentication > URL Configuration**:

- **Site URL**: your Netlify link, for example `https://yourname.netlify.app`
- **Redirect URLs**: add the same link, and add `https://yourname.netlify.app/**`

## 4. Email and password sign-in

Email sign-in is on by default. In **Authentication > Sign In / Providers > Email**:

- **Confirm email** on: new people must tap a link in their inbox before signing in. Supabase's built-in sender only allows a few emails per hour, so for real use add your own email service under **Authentication > SMTP Settings**.
- **Confirm email** off: people can sign in straight away. Handy while you test.

"Forgot your password?" on the sign-in screen sends a reset link and works with either setting.

## 5. "Continue with Google" button

1. Go to console.cloud.google.com, create a project, then open **APIs & Services > OAuth consent screen** and set it up (External, add your app name and email).
2. Open **Credentials > Create credentials > OAuth client ID**, choose **Web application**.
3. Under **Authorized redirect URIs** add `https://YOUR-PROJECT-REF.supabase.co/auth/v1/callback`. Supabase shows this exact address in **Authentication > Sign In / Providers > Google**.
4. Copy the **Client ID** and **Client secret** into that same Supabase Google screen, switch it on, and save.

Until you finish this step the Google button will not work, but email sign-in still does.

## 6. Test it with two people

Use two different accounts, for example your phone and a friend's.

1. Both sign in and choose a name and username.
2. Person A: Friends tab, add Person B's username.
3. Person A: create an event, open it, tap **Invite friends**, tick Person B.
4. Person B: the event appears under **Invited**. Reply Going, then send a gift.
5. Person A: the gift and the reply show on the event. Only A sees the amounts.
6. Try the code route: Person B opens the event code from A's Overview tab. An invite-only event shows a lock and "Ask to join", and A approves from the Guests tab.

## Paid-ticket events

When creating an event, choose **Paid tickets** at the top. Set the ticket price (KES) and the number of tickets. Anyone with the event code or link can open it and buy as many tickets as are left. Guests see "X of N tickets left", and a big **SOLD OUT** banner when none remain. You see every buyer and ticket code under the **Tickets** tab and can cancel any ticket there, which puts the spot back on sale.

If you already set the app up earlier, open Supabase **SQL Editor**, paste the new `supabase/schema.sql` and **Run** it again. It is safe to re-run and keeps your existing events.

Ticket payments are a placeholder for now: buying records the tickets without charging anyone. When M-Pesa is added, call `buy_ticket` from the Edge Function only after the payment succeeds, and remove the client-side call.

## Things to know

- **Payments are not connected.** Sending a gift or buying a ticket records it without charging anyone. M-Pesa payment needs a small server step (a Supabase Edge Function) and should create the gift record only after the payment succeeds. Until then anyone can record any gift price.
- **Photos** are stored inside the event record, resized small. Move them to Supabase Storage when you have many events.
- **Names and usernames** are readable by any signed-in person. Emails are never exposed.
- **Reminders** to guests are not sent. Sending them needs a scheduled server job.
- To update the app later, change `index.html` and drag the folder onto Netlify again.
