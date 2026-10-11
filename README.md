# OMD Tech — Android releases

This repository holds the **signed OMD Tech Android builds**.

The download that customers use lives on our own domain and streams the file from
here server-side, so nobody is bounced to GitHub mid-download:

**https://omdtech.vercel.app/app-download**

The file it serves is [`omd-tech-latest.apk`](https://github.com/Brave290/omd-tech-android-releases/raw/main/omd-tech-latest.apk)
at the repository root. It is always the newest signed release.

## What is in here

| Path | What it is |
| --- | --- |
| `omd-tech-latest.apk` | The current build. Overwritten on every release. This is what the website serves. |
| `latest.json` | Machine-readable pointer: `version`, `publishedAt`, `apkUrl`, `aabUrl`. |
| `releases/omd-tech-<version>.apk` | Signed, installable APK for that version. |
| `releases/omd-tech-<version>.aab` | Signed Android App Bundle for that version (Play Store upload). |

## Installing

Download `omd-tech-latest.apk`, allow your browser to install unknown apps the
first time, then open the file and choose **Install**. The build is signed with
our release certificate, so Android will not warn about an unknown developer.

Requires Android 8.0 or newer. No Google Play account needed.

## How these files get here

Every file is published by a GitHub Actions workflow running in the **main
repository** (`Brave290/omd-tech`), not by hand. When a release is published in
the main repo, `.github/workflows/publish-android-release.yml`:

1. downloads the `omd-tech-<version>.apk` and `omd-tech-<version>.aab` assets
   from that release,
2. commits them into `releases/` here,
3. overwrites `omd-tech-latest.apk` at the root,
4. rewrites `latest.json` with the new version, timestamp and URLs.

So the main repository is the source of truth for builds. This repository is a
distribution mirror for the one file the public website needs to serve.

## ⚠️ Automated publishing requires a secret

Publishing is automated, but it will **fail** until a token secret exists. There
is no way around this: GitHub Actions in the main repo cannot push to a
*different* repository using the default `GITHUB_TOKEN`, which is scoped to the
repository the workflow runs in.

**Required:** add a repository secret named `RELEASE_REPO_TOKEN` under
`Brave290/omd-tech` → **Settings** → **Secrets and variables** → **Actions**.

It must be a **classic personal access token** (`ghp_...`), not a fine-grained
token. Fine-grained tokens are not supported for this workflow.

- **Scope:** `repo` (full control of private repositories). The token owner needs
  write access to `Brave290/omd-tech-android-releases`.
- **Why classic:** the `repo` scope covers the public repositories the token can
  write to. A fine-grained token restricted to the main repo cannot push here.
- **Where to create it:** https://github.com/settings/tokens/new
  (`repo` scope, then Generate token → copy it once).

Until that secret exists, releases publish normally to the main repo but this
mirror goes stale, and the website will keep serving the previous build. Nothing
breaks; the download simply stops updating.

**Treat the token as a password.** Anyone holding it can push to this
repository. Revoke it at https://github.com/settings/tokens if it leaks.

## License

The builds are proprietary. Copyright © OMD Tech.