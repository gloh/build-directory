# Fork the CRS Sales App

You are helping the user fork and deploy their own copy of the CRS Sales App — an interactive sales presentation site built with HTML, CSS, and JavaScript. Follow every step below autonomously. Ask the user only when a step explicitly says to.

## Step 1: Clone the source repo

```
git clone https://github.com/gloh/crs-sales-app.git my-sales-app
cd my-sales-app
```

## Step 2: Detach from the original repo

Remove the original git history so this becomes the user's own project:

```
rm -rf .git
git init
git add -A
git commit -m "Initial commit — forked from CRS Sales App"
```

## Step 3: Create a new GitHub repo

Ask the user: **"What should I name your new GitHub repo?"** (e.g. `my-sales-app`)

Then create it:

```
gh repo create <repo-name> --public --source=. --push --remote origin
```

If `gh` is not authenticated, tell the user to run `gh auth login` first and then retry.

## Step 4: Deploy to Vercel

Ask the user: **"Would you like me to deploy this to Vercel now? You'll need the Vercel CLI installed (`npm i -g vercel`)."**

If yes:

```
vercel --yes
vercel --prod
```

This will create a new Vercel project and deploy it. The user will get a live URL.

If the user doesn't have Vercel CLI or prefers the dashboard, tell them:
> Go to https://vercel.com/new, import your new GitHub repo, and click Deploy. No build settings needed — it's a static site.

## Step 5: Confirm and hand off

Tell the user:
- Their forked project is at `~/my-sales-app` (or whatever directory was used)
- It's connected to their own GitHub repo
- It's deployed on Vercel (share the URL)
- They can now make any changes to `index.html` and push to redeploy
- The site is a single `index.html` file with inline CSS/JS — no build step, no dependencies

**They now fully own this project and can customize it however they want.**
