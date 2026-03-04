# GitHub Profile README Stats

A dynamic GitHub profile README that displays a neofetch-style stats card. The card auto-updates daily via GitHub Actions, pulling live data from the GitHub GraphQL API.

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/muhammadbalawal/muhammadbalawal/main/dark_mode.svg">
  <img alt="Example stats card" src="https://raw.githubusercontent.com/muhammadbalawal/muhammadbalawal/main/light_mode.svg">
</picture>

## What It Shows

- **Age** -- time since your birthday, formatted as years/months/days
- **Commits** -- total commits across all repositories
- **Stars** -- total stars on repositories you own
- **Repositories** -- owned and contributed repo counts
- **Followers**
- **Lines of Code** -- additions, deletions, and net LOC across all repos you've authored commits in

## Project Structure

```
.
├── main.py                  # Core script: fetches stats and updates SVGs
├── dark_mode.svg            # SVG template (dark theme)
├── light_mode.svg           # SVG template (light theme)
├── README.md                # Profile README (displays the SVG cards)
├── LICENSE                  # MIT License
├── FORK.md                  # This file
├── cache/
│   ├── requirements.txt     # Python dependencies
│   ├── <hash>.txt           # LOC cache (per-user, auto-generated)
│   └── repository_archive.txt  # Archived stats for deleted repos
└── .github/
    └── workflows/
        └── build.yaml       # GitHub Actions workflow (daily cron + push)
```

## Fork and Set Up Your Own

### 1. Fork the Repository

Click **Fork** on GitHub to create your own copy.

### 2. Create a GitHub Personal Access Token

Create a **Fine-Grained Personal Access Token** with **All Repositories** access and the following permissions:

| Scope | Permissions |
|-------|-------------|
| Account | `read:Followers`, `read:Starring`, `read:Watching` |
| Repository | `read:Commit statuses`, `read:Contents`, `read:Metadata` |

### 3. Add the Token as a Repository Secret

Go to your forked repo's **Settings > Secrets and variables > Actions** and add a new secret:

- **Name:** `ACCESS_TOKEN`
- **Value:** your personal access token from step 2

### 4. Update `main.py` with Your Info

Open `main.py` and change these values:

```python
USER_NAME = 'muhammadbalawal'  # Change to your GitHub username
```

In the `__main__` block, update the birthday:

```python
age_data, age_time = perf_counter(daily_readme, datetime.datetime(2005, 9, 18))
#                                                 ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
#                                                 Change to your birthday
```

The `OWNER_ID` check on the line starting with `if OWNER_ID ==` is specific to the original author and loads archived data for deleted repos. You should **remove or comment out** that entire `if` block unless you have your own archive data.

### 5. Customize the SVG Templates

Edit `dark_mode.svg` and `light_mode.svg` to personalize:

- Your name and display info
- OS, host, kernel, IDE fields
- Languages, hobbies, and contact links
- Color scheme

The stats fields (commits, stars, LOC, etc.) are updated automatically via element IDs like `commit_data`, `star_data`, `loc_data`, etc.

### 6. Delete the Existing Cache

Delete the file inside `cache/` that has a long hash name (e.g., `cache/943d8e...txt`). A new one will be auto-generated for your username on the first run.

### 7. Push to `main`

The GitHub Action triggers on every push to `main` and runs daily at 05:00 UTC. You can also trigger it manually from the **Actions** tab.

## How the Caching Works

Fetching lines of code across all repositories is expensive (one GraphQL query per 100 commits, per repo). To avoid redundant API calls:

1. On first run, the script queries every repository and stores the commit count and LOC per repo in a cache file (`cache/<sha256_of_username>.txt`).
2. On subsequent runs, it only re-fetches LOC for repos whose commit count has changed.
3. If the total number of repositories changes (new repo created/deleted), the cache is flushed and rebuilt.

The `repository_archive.txt` file preserves stats from repositories that have been deleted.

## Dependencies

Install via pip:

```bash
pip install -r cache/requirements.txt
```

| Package | Purpose |
|---------|---------|
| `python-dateutil` | Age calculation with relative deltas |
| `requests` | HTTP requests to GitHub GraphQL API |
| `lxml` | SVG parsing and element text replacement |

## Running Locally

```bash
export ACCESS_TOKEN="your_github_token"
export USER_NAME="your_username"
python main.py
```

## License

This project is licensed under the [MIT License](LICENSE).
