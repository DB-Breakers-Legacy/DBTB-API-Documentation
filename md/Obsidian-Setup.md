# Obsidian vault and Git setup

This directory is both the Obsidian vault root and the Git repository root. Open this directory directly in Obsidian rather than opening its parent directory.

## First-time setup

1. In Obsidian, choose **Open folder as vault** and select `DBTB-API`.
2. Open **Settings → Community plugins**, turn on community plugins, and install **Git** by Vinzent.
3. Enable the Git plugin. The vault already contains shared settings for a commit-and-sync operation ten minutes after editing stops.
4. Open the command palette and run **Git: Edit remotes**. Add your documentation repository as the `origin` remote.
5. Configure authentication on the device. Do not save a password, token, private key, or other credential in this vault.
6. Run **Git: Commit-and-sync** once and confirm that the push succeeds.

The shared profile commits all changed vault files, pulls before pushing, uses the `merge` sync method, and leaves startup pulls disabled so a fresh checkout does not pull before its remote is configured. Once `origin` works, enable **Pull on startup** in the Git plugin settings if desired.

## What is shared

- Markdown documentation and attachments
- Portable relative Markdown links
- Vault-wide Obsidian settings
- The enabled-plugin list and Git plugin settings

Device-specific workspace layouts, caches, trash, and downloaded plugin program files are ignored. Each device must install the Git plugin through Obsidian.

## Working on multiple devices

Finish a session with **Git: Commit-and-sync**, especially before changing devices. Git is asynchronous version control rather than live co-editing, so avoid editing the same note independently on two devices before syncing.

If a pull reports a conflict, resolve the marked files before committing or pushing again. Do not choose a conflict strategy that automatically discards one side unless losing those edits is intentional.

## Reference

- [Obsidian Git plugin](https://github.com/Vinzent03/obsidian-git)
- [Installation](https://publish.obsidian.md/git-doc/Installation)
- [Getting started](https://publish.obsidian.md/git-doc/Getting+Started)
