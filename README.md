# Bridge House — Listing Manager Backend

A small, free backend that lets a non-technical person add real property
listings (photos, bedrooms/bathrooms, price, description, and an actual pin
on a map) through a simple web form — no HTML editing required.

It gives you:

- **`/admin`** — a password-protected panel to add / edit / delete listings,
  upload multiple photos per property, and drop a pin on a map (click to
  place it, or drag it).
- **`/`** and **`/listings/:id`** — public pages that show the live listings
  with photo gallery + embedded map, usable on their own.
- **`/api/listings`** — a public JSON endpoint your existing GitHub Pages
  site (`adriaticahomes`) can fetch from, so the listings can appear inside
  your current design too (see `embed-snippet.html`).

Everything below is **free**, using free tiers of three services:

| Service | What it's for | Free tier |
|---|---|---|
| [Render](https://render.com) | Runs this backend | Free Web Service |
| [MongoDB Atlas](https://www.mongodb.com/cloud/atlas/register) | Stores listing data | Free M0 cluster (512MB, permanent) |
| [Cloudinary](https://cloudinary.com) | Stores & serves photos | Free tier (permanent) |

Render's free web service has **no persistent disk** — anything written to
its own filesystem is lost on restart. That's why listing data lives in
MongoDB Atlas and photos live in Cloudinary, both of which persist
independently of Render.

---

## 1. Create a free MongoDB Atlas database

1. Go to https://www.mongodb.com/cloud/atlas/register and create a free account.
2. Create a free **M0** cluster (any region close to Italy/Europe is fine).
3. Under **Database Access**, create a database user with a username and password.
4. Under **Network Access**, click **Add IP Address** → **Allow access from anywhere** (`0.0.0.0/0`) — needed so Render can connect.
5. Click **Connect** → **Drivers**, and copy the connection string. It looks like:
   ```
   mongodb+srv://USERNAME:PASSWORD@cluster0.xxxxx.mongodb.net/?retryWrites=true&w=majority
   ```
6. Add a database name to it, e.g. `.../adriaticahomes?retryWrites=true&w=majority`.
   This full string is your `MONGODB_URI`.

## 2. Create a free Cloudinary account

1. Go to https://cloudinary.com/users/register/free and sign up.
2. On your Dashboard you'll see **Cloud name**, **API Key**, and **API Secret** —
   you'll need all three.

## 3. Put this code on GitHub

Create a **new, separate** repository for this backend (don't mix it into
`adriaticahomes`, which is your static front-end). For example
`adriaticahomes-backend`. Push all the files in this folder to it.

## 4. Deploy to Render (free)

1. Go to https://render.com and sign up / log in (free).
2. Click **New** → **Web Service**, connect your GitHub account, and pick the
   `adriaticahomes-backend` repo.
3. Settings:
   - **Build Command:** `npm install`
   - **Start Command:** `npm start`
   - **Instance Type:** Free
4. Under **Environment**, add these variables:

   | Key | Value |
   |---|---|
   | `MONGODB_URI` | the connection string from step 1 |
   | `CLOUDINARY_CLOUD_NAME` | from step 2 |
   | `CLOUDINARY_API_KEY` | from step 2 |
   | `CLOUDINARY_API_SECRET` | from step 2 |
   | `ADMIN_PASSWORD` | a password you choose, for logging into `/admin` |
   | `SESSION_SECRET` | any long random string |
   | `ALLOWED_ORIGIN` | `https://dragonkrakow.github.io` |

5. Click **Create Web Service**. After a couple of minutes you'll get a URL
   like `https://adriaticahomes-backend.onrender.com`.

   Note: on the free plan, the service "sleeps" after ~15 minutes without
   traffic and takes ~30–50 seconds to wake up on the next visit. That's
   normal for free hosting and fine for a low-traffic listings site.

## 5. Add listings

1. Visit `https://YOUR-APP.onrender.com/admin`.
2. Log in with the `ADMIN_PASSWORD` you set.
3. Click **+ Dodaj nową ofertę** (Add new listing), fill in the details,
   click on the map to place the pin, upload photos, and save.

Anyone you give the admin URL + password to can now add listings without
touching any code.

## 6. (Optional) Show listings on your existing GitHub Pages site

Open `embed-snippet.html` in this project. It's a self-contained block of
HTML/CSS/JS that fetches `/api/listings` and renders a grid of listing
cards. To use it:

1. Edit the line `var API_BASE = "https://YOUR-RENDER-APP.onrender.com";`
   to your real Render URL.
2. Paste the whole snippet into your `adriaticahomes` `index.html`, in the
   "Dostępne Rezydencje Premium" section, replacing the empty placeholder
   card that's there now.
3. Commit and push — GitHub Pages will redeploy automatically, and from
   then on, every listing added in `/admin` will appear on your live site
   with no further HTML edits.

Alternatively, you can simply link to `https://YOUR-RENDER-APP.onrender.com/`
directly (e.g. as the "Zobacz Mapę Ofert" button target) — it's a complete,
styled public listings page + per-property page on its own.

## Local development (optional)

```bash
cp .env.example .env   # fill in the values
npm install
npm start
```
Then open http://localhost:3000 and http://localhost:3000/admin.

## Notes on the admin password

This uses one shared password (no individual user accounts) — intentionally
simple, since the goal was "easy and free." If you later want individual
logins, an activity log, or stronger security, that can be added on top of
this same structure.
