# Windows

Collection of Windows troubleshooting guides, fixes and tips, based on real step-by-step conversations. Includes PowerShell and command-line solutions for Windows 10/11.

> By Claude Sonnet 5.5

Each guide documents a real problem: the error, the cause, the quick fix and the steps that were actually tried.

---

## Index

| Topic | Description | Tested on |
|---|---|---|
| [winget](winget/) | Fix `winget` is not recognized: repair App Installer and install software from the command line | Windows 11 Home 26H2 |

> More guides will be added over time.

---

## Repository structure

```
windows/
├── README.md                  ← this index
├── winget/
│   └── README.md
└── <topic>/
    └── README.md
```

- Each topic lives in its own folder at the root of the repository.
- Folder names are lowercase and hyphenated (e.g. `winget`, `slow-boot`).
- Every guide starts with a short summary (problem, cause, quick fix, tested on) followed by the details.

---

## How to use these guides

1. Find your problem in the [index](#index), or search for the exact error message.
2. Read the summary at the top of the guide.
3. Follow the steps in order, stopping as soon as the problem is solved.

Most commands are meant to be run in **PowerShell**. Where administrator rights are needed, the guide says so.

---

## Disclaimer

These guides come from specific cases and were tested on the Windows version listed in each one. Results may vary on other versions or configurations. Review any command before running it, and back up important data before making system changes. Use at your own risk.

---

## Privacy

Usernames, emails and other personal data have been removed or replaced (e.g. `<user>`) in all guides and screenshots.

---

## Contributing

Found a mistake or have a fix that worked for you? Open an issue or a pull request. If you want to add a new guide, create a folder at the root of the repository with a `README.md` following the same structure as the existing ones.
