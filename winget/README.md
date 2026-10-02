# Fix: `winget` is not recognized on Windows 11

**Problem:** Running `winget` in Command Prompt or PowerShell returns an error saying the command is not recognized.

**Cause:** The *App Installer* component (which provides `winget`) is missing, unregistered, corrupted or outdated, even on recent Windows 11 versions.

**Quick fix:** Repair WinGet from PowerShell with the official `Microsoft.WinGet.Client` module (see [Solution](#solution)).

**Tested on:** Windows 11 Home, version 26H2 (OS build 26300.9550)

---

## Error messages

Command Prompt (English):

```
'winget' is not recognized as an internal or external command,
operable program or batch file.
```

Command Prompt (Spanish):

```
"WINGET" no se reconoce como un comando interno o externo,
programa o archivo por lotes ejecutable.
```

PowerShell (English):

```
winget : The term 'winget' is not recognized as the name of a cmdlet, function, script file, or operable program.
```

PowerShell (Spanish):

```
winget : El término 'winget' no se reconoce como nombre de un cmdlet, función, archivo de script o programa ejecutable.
```

> Note: command names are case-insensitive in Windows, so `WINGET` vs `winget` is not the problem.

---

## Possible causes

1. **App Installer is not installed or is outdated.** `winget` depends on it (Windows 10 1809+ and Windows 11 should include it).
2. **LTSC / Server editions** do not ship with `winget` or the Microsoft Store.
3. **App execution alias is disabled** for *App Installer / winget.exe*.
4. **Terminal not restarted** after installing or repairing.

---

## Solution

Open **PowerShell** (not Command Prompt) and follow the steps in order, stopping when `winget --version` works.

### 1. Check whether App Installer is installed

```powershell
Get-AppxPackage -Name Microsoft.DesktopAppInstaller
```

- No output → it is not installed, go to step 2.
- Output shown → it is installed, but the alias/PATH may be failing. Check the alias (see [Extra checks](#extra-checks)).

### 2. Repair WinGet (official method)

Paste the lines one by one:

```powershell
Install-PackageProvider -Name NuGet -Force | Out-Null
Install-Module -Name Microsoft.WinGet.Client -Force -Repository PSGallery | Out-Null
Repair-WinGetPackageManager -AllUsers
```

If PowerShell asks you to trust the PSGallery repository, answer `Y`.

### 3. Restart the terminal

Close **all** PowerShell / Command Prompt windows and open a new one. Then:

```powershell
winget --version
```

If it prints a version number (e.g. `v1.x.xxxxx`), it works.

### 4. First use

```powershell
winget search vlc
```

The first time, you may need to accept the source agreements (`msstore` and `winget`). Type `Y` and press Enter.

---

## Extra checks

If the repair did not work:

- **Re-register App Installer:**
  ```powershell
  Add-AppxPackage -RegisterByFamilyName -MainPackage Microsoft.DesktopAppInstaller_8wekyb3d8bbwe
  ```
- **Update from the Microsoft Store:** search for *App Installer* and click *Update*.
- **Check the execution alias:** *Settings → Apps → Advanced app settings → App execution aliases*, and make sure *App Installer (winget.exe)* is on.
- **Manual install:**
  ```powershell
  cd $env:TEMP
  Invoke-WebRequest -Uri https://aka.ms/getwinget -OutFile winget.msixbundle
  Add-AppxPackage winget.msixbundle
  ```
  If it fails with dependency errors, they usually mention `VCLibs` or `UI.Xaml`.
- **Sign out and back in** (or reboot) so Windows reloads the PATH and aliases.
- **Check your Windows version** with `Win + R` → `winver`.

---

## Using winget

Install VLC:

```powershell
winget search vlc
winget install VideoLAN.VLC
```

Useful commands:

| Command | What it does |
|---|---|
| `winget upgrade` | Lists programs with available updates |
| `winget upgrade --all` | Updates everything at once |
| `winget uninstall <name>` | Uninstalls a program |

---

## Is it better to install and update programs with winget?

For most people, yes, with some caveats.

**Pros**
- Fast: install or update several programs with a single command, no "Next, Next" wizards.
- Safer than searching the web: packages are validated and point to the vendor's official installer, avoiding fake sites and adware.
- `winget upgrade --all` centralizes updates instead of each program having its own updater.
- Handy when reinstalling a PC: you can keep a list of programs and reinstall them in one go.

**Limitations**
- Not every program is in the repository, and new versions can take a few days to appear.
- It does not update everything: some programs installed by other means do not show up in `winget upgrade`.
- Updates can fail or ask you to close the program if it is in use.
- No graphical interface.

**Recommendation:** use winget for common software (VLC, browsers, 7-Zip, etc.) and run `winget upgrade` now and then (for example monthly) to review what is pending before using `--all`. For anything not in the repository, download from the official website and avoid unknown sources.

---

## Summary of the conversation

1. The user ran `winget search vlc` and got "not recognized".
2. Windows version was checked with `winver`: Windows 11 Home 26H2, recent enough to include winget, so the issue was with App Installer, not the Windows version.
3. `winget --version` in PowerShell also failed.
4. Ran the official repair (`Repair-WinGetPackageManager -AllUsers`) with no errors.
5. After restarting the terminal, `winget` worked.

---

## Privacy note

Usernames in paths have been replaced by `<user>` and personal data has been removed from screenshots.

> By Claude Sonnet 5.5
