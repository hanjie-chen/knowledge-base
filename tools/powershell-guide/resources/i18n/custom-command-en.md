---
Title: PowerShell Custom Commands
SourceBlob: 50f561566451affba270c782a430e06a8cd7b569
---

```
BriefIntroduction: PowerShell custom commands usage note.
```

<!-- split -->

# Profile Source of Truth

These custom commands come from my PowerShell `$PROFILE`.

The `$PROFILE` file is no longer maintained as copied code in this knowledge-base article. It is managed by [personal-config](https://github.com/hanjie-chen/personal-config). The actual implementation lives at:

```text
personal-config/powershell/Microsoft.PowerShell_profile.ps1
```

On my Windows machine, the PowerShell 7 profile points to that file through a symbolic link:

```text
$env:USERPROFILE\Documents\PowerShell\Microsoft.PowerShell_profile.ps1
```

This article only records what the commands are for and how I commonly use them. To change the implementation, edit the profile file in `personal-config`.

# Command `open`

`open` opens a file or directory from PowerShell.

For directories, it uses Windows Explorer. For files, it tries to behave closer to double-clicking the file from Explorer, so GUI apps are less likely to stay attached to the Windows Terminal session and close together with the terminal.

Common usage:

```powershell
open .
open .\notes.md
open .\some-folder
```

I usually use it to quickly open the current directory, Markdown files, or files that should be viewed or edited with their default app.

# Command `touch`

`touch` mimics the Linux `touch` command.

If a file does not exist, it creates it. If the file already exists, it updates the file's `LastWriteTime`.

Common usage:

```powershell
touch note.md
touch .env
touch a.txt b.txt
```

This is useful for quickly creating temporary files, initializing Markdown notes, or refreshing file timestamps.

# Command `tree`

`tree` prints a directory tree for the current path.

It overrides PowerShell's original `tree` command and supports options that are more convenient for daily use, such as showing directories only or ignoring selected names.

Common usage:

```powershell
tree
tree -DirectoriesOnly
tree -Ignore "resources", "images"
```

`-DirectoriesOnly` shows directories only. `-Ignore` skips names that I do not want to inspect.

The command also works with `$PSStyle.FileInfo` color settings for directories, symbolic links, executable files, and several common file extensions, making the tree output easier to scan.
