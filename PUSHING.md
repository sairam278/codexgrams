# How to push local code to this repository

Replace `main` with your default branch name if different.

## New local project (start from a folder with your code)

### HTTPS
```bash
git init
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin https://github.com/sairam278/codexgrams.git
git push -u origin main
```

### SSH (if you have an SSH key added to GitHub)
```bash
git init
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin git@github.com:sairam278/codexgrams.git
git push -u origin main
```

## Existing local repository (you already have commits)

### Add the remote and push the current branch as `main` (HTTPS)
```bash
git remote add origin https://github.com/sairam278/codexgrams.git
git branch -M main
git push -u origin main
```

### Or using SSH
```bash
git remote add origin git@github.com:sairam278/codexgrams.git
git branch -M main
git push -u origin main
```

## Push all branches and tags (optional)
```bash
git push --all origin
git push --tags
```

## Clone the repo
### HTTPS
```bash
git clone https://github.com/sairam278/codexgrams.git
```

### SSH
```bash
git clone git@github.com:sairam278/codexgrams.git
```

## Notes
- For HTTPS pushes you may need a GitHub personal access token (PAT) for authentication — use the token as the password if prompted.
- For SSH, ensure your public SSH key is added to your GitHub account.
- If you get an error like “remote origin already exists”, run:
```bash
git remote remove origin
```
then re-run the `git remote add` command above.
