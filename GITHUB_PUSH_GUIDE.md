# 🐙 Pushing Zynta to GitHub — Step by Step

---

## 0. One-time sanity check before you push anything

From the project root:
```bash
ls backend/.env          # should say "No such file or directory" — good, it's gone
cat backend/.env.example # should show placeholders only, no real password
```
If `backend/.env` exists in your copy, delete it before continuing — it should
never be committed:
```bash
rm -f backend/.env
```

---

## 1. Create the repository on GitHub

1. Go to https://github.com/new
2. **Repository name:** `zynta`
3. **Description** — paste this in:

   > Full-stack multi-role e-commerce marketplace (React + Node.js/Express + PostgreSQL) with seller onboarding, product catalog, cart, and order management, secured with JWT.

4. **Visibility:** Public
5. Do **NOT** check "Add a README" / "Add .gitignore" / "Add a license" — you already have these locally.
6. Click **Create repository** and keep the page open for the remote URL, e.g. `https://github.com/<your-username>/zynta.git`

---

## 2. Start a fresh git history locally

From the project root (the folder containing `backend/`, `frontend/`, `README.md`):

```bash
cd zynta

# Remove any leftover .git folder from the old clone so history doesn't carry over
rm -rf .git

git init
git add .
git status   # <-- review this list before committing
```

**Check the `git status` output carefully.** You should NOT see:
- `frontend/node_modules/...` or `backend/node_modules/...`
- `backend/.env` or `frontend/.env`
- `.idea/`, `.vscode/`

If you see `TODO.md` staged and you'd rather keep it private, it's already
listed in `.gitignore` — if it still shows up, run `git rm --cached TODO.md`.

Once the list looks clean:
```bash
git config user.name "Your Name"
git config user.email "your_email@example.com"
git commit -m "Initial commit: Zynta e-commerce platform"
```

---

## 3. Connect to GitHub and push

```bash
git branch -M main
git remote add origin https://github.com/<your-username>/zynta.git
git push -u origin main
```

If prompted for credentials with 2FA enabled, use a
[Personal Access Token](https://github.com/settings/tokens) instead of your
password, or push via SSH if you have a key set up with GitHub.

---

## 4. Verify on GitHub

Open `https://github.com/<your-username>/zynta` and confirm:
- [ ] `node_modules/` is **not** present anywhere
- [ ] No `.env` file exists in `backend/` or `frontend/`
- [ ] `backend/.env.example` shows placeholders only
- [ ] `backend/SETUP.md` no longer shows a real Supabase hostname
- [ ] Only **one commit** exists in the history (Insights → Commits)
- [ ] `README.md` renders correctly on the repo homepage

---

## 5. What to send the interviewer

Just the repository URL:
```
https://github.com/<your-username>/zynta
```

---

## Troubleshooting

**"`node_modules` still shows up in `git status`"**
Fix:
```bash
git rm -r --cached frontend/node_modules backend/node_modules
git commit -m "Remove accidentally tracked build artifacts"
```

**"I want to credit my friend without exposing their personal email"**
Add a short line in the README's Author/Acknowledgments section with just
their name (no email), e.g. "Built with [Friend's Name] as a group project."
Their commits and email from the original repo won't appear here since this
history was reset.

**"I already pushed and now realize a real secret is in the history"**
Rotate the exposed credential immediately (change the DB password / regenerate
the JWT secret) — changing the file in a new commit does not remove it from
git history. For this repo specifically, since it's a fresh single-commit
history, this shouldn't happen — but it's good practice to know for future
projects.
