# Git Workflow — LFCS Notes Repo

## My Repo
- **Remote:** https://github.com/kennethlcrow/lfcs-notes
- **View at work:** Browse to the repo on GitHub — `.md` files render automatically in the browser
- **Local path:** `C:\Users\luffy\Documents\Second Brain\LFCS`

## Keeping Notes Updated

After adding or editing notes:

```bash
git add .
git commit -m "Brief description of what you updated"
git push
```

## Useful Commands

```bash
git status          # see what's changed since last commit
git log --oneline   # see recent commits
git diff            # see exact line changes before staging
```

## Notes
- `.obsidian/` is gitignored — personal settings/plugins won't be pushed
- Commit messages don't need to be fancy — "Add systemd notes" is fine
- Push regularly so work access is always up to date
