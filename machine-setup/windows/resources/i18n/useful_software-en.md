---
Title: Additional Windows Software and Software Settings
SourceBlob: 12f70582bde3c06ccda056a5b2f15ee05eed82da
---

```
BriefIntroduction: After reinstalling Windows and finishing the native system settings, there are some extra programs worth downloading to make the system feel smoother to use. I personally prefer open-source software.
```

<!-- split -->

# Before We Start

I recently built a new PC and downloaded some extra software to make it easier to use. This article is meant to help me restore the computer to the familiar style and setup I like after reinstalling the system. It may also serve as a reference for others with similar needs.

# Additional Windows Software

These are some programs I currently use to make Windows more convenient.

| Software name                | Description                                                  |
| ---------------------------- | ------------------------------------------------------------ |
| Clash-for-window             | Without this, I cannot even download Chrome                  |
| Chrome Browser               | The strongest browser on the planet, used to download other software |
| 7-zip                        | Open-source compression and decompression software for Windows |
| everything                   | Essential for finding files                                  |
| Vmware-workstation           | Virtual machine software; I need one Windows 10 VM for playing galgames and one Linux VM for development |
| PotPlayer                    | Video player                                                 |
| Typora                       | A great Markdown writing tool. I started using it from the free 0.7 version and eventually became a paid user |
| YACReader                    | A great local comic reader that can also create a local comic library |
| Visual Studio code (VS code) | A lightweight code editor that works very well on both Windows and macOS |
| Steam                        | Essential for entertainment                                  |
| Windows terminal             | An excellent terminal tool for Windows; use it with oh-my-posh for the best experience |
| Fliqlo                       | Desktop screensaver                                          |
| Mircosoft Todo List          | Set daily tasks                                              |
| Koodo Reader                 | Open-source reading software for Windows                     |
| codex                        | A powerful tool that tries to help you solve any problem     |
| rime                         | Weasel, an excellent input method                            |
| python3                      | A dependency for writing small utilities, replacing anaconda3, which is too heavy |
| git                          | Version control for notes and code, usually synced to GitHub |

# Software Settings

## Rime

When we use `fu pan` to try to type “复盘”, we may find that it fails because of a bug related to Traditional Chinese to Simplified Chinese conversion.

We can create a `custom_phrase.txt` file in the user folder with the following content:

```txt
复盘	fupan	100
复盘	fp	100
```

Note: the separator in the middle must be a Tab, not spaces.

Then right-click Weasel and redeploy the service.

### Sync Backup

The official multi-device sync method recommended by Rime is to set `sync_dir` in `installation.yaml`, then use “Sync user data”.

The official documentation states this clearly: during sync, user dictionaries are generated or merged into `*.userdb.txt` snapshots, and non-auto-generated YAML and `.txt` files in the user folder are backed up one-way to the sync directory. Typical files in the sync directory include `installation.yaml`, `default.custom.yaml`, `weasel.custom.yaml`, and `*.userdb.txt`.

1. Open `installation.yaml` in the user folder
2. Add a line, for example:

```yaml
sync_dir: 'D:\OneDrive\backup\Rime\backup'
```

1. Save and redeploy
2. Run “Sync user data” in Weasel

Note: syncing user data includes two actions: pull + push.

It first pulls existing user dictionary snapshots from the sync directory and merges them into the current dictionary, then pushes the current user configuration and new dictionary snapshot back to the sync directory. If the sync directory is empty, it simply backs up the current configuration there; if it is not empty, it merges first and then backs up.

## Edge Settings

Purpose: prevent searches from redirecting to `cn.bing.com`, and use New Bing instead.

As the browser bundled with Windows, Edge is sometimes not as good as Chrome, but it is still good enough for some everyday use.

The first issue is that when searching with Bing, the region is forcibly set to China, which prevents access to useful AI tools such as New Bing.

The cause is the search engine setting for the search bar. Go to Edge Settings --> Privacy, search, and services --> Address bar and search --> Manage search engines, as shown below.

![search-engine-preview](./resources/images/search-engine-preview.png)

Just modify the query URL, or add a new one and set it as the default.

![search-engine-current](./resources/images/search-engine-current.png)

That is all. I referenced this article[^1].

1. Vertical tabs

   <img src="./resources/images/vertical-tab.png" alt="picture" style="zoom:60%;" />

2. Hide title bar

   <img src="./resources/images/hidden-tablename.png" alt="picture" style="zoom:60%;" />

3. Other settings

   You can explore other homepage settings yourself.

## Fliqlo Settings

Purpose: use a global shortcut key to launch the desktop screensaver. Note that it does not work when software is in fullscreen mode.

### Method 1: Pin the Fliqlo Shortcut to the Start Menu

1. Create a Fliqlo shortcut and place it on the desktop
2. Open the Start Menu folder. This folder is usually located at `C:\ProgramData\Microsoft\Windows\Start Menu\Programs`. Drag the shortcut into it; administrator permission is required
3. Open the Start Menu, find the Fliqlo shortcut, right-click it, and pin it to Start

### Method 2: Create a Desktop Shortcut Directly and Set a Shortcut Key

1. Note that this method cannot hide the desktop shortcut. Otherwise, after the next startup, the shortcut key will no longer work. This approach leaves a shortcut on the desktop, but I prefer a clean desktop, so I gave up on this solution



## Windows Terminal Optimization

Purpose: beautify the terminal and enable built-in administrator privileges for the terminal.

The main approach is to use the [oh-my-posh](https://ohmyposh.dev/) theme to optimize Windows Terminal.

Then there are some Windows Terminal visual improvements and command-line completion settings[^2].

```josn
        "defaults": {
          // start transparent effect, set the transparency to 0.9
          "useAcrylic": true, 
          "acrylicOpacity": 0.9,
          "fontFace": "MesloLGM Nerd Font Mono",
          "fontSize": 14
        },
```

For details, see the reference video.

Next is enabling administrator privileges by default. Use the `elevate` property so it opens automatically as administrator[^3]. Also, set the startup directory to the project folder by default: `E:\\Personal_Project`.

The JSON code is as follows:

```json
            {
                "commandline": "%SystemRoot%\\System32\\WindowsPowerShell\\v1.0\\powershell.exe",
                "guid": "{61c54bbd-c2c6-5271-96e7-009a87ff44bf}",
                "hidden": false,
                "name": "Windows PowerShell",
                // open the wt default as the administer
                "elevate": true,
                // set the default start directory
                "startingDirectory": "E://Personal_Project"
            }
```

## PotPlayer Settings

Purpose: make the player easier to use.

This software has far too many configurable options. The most important ones are hardware decoding, or H/W GPU decoding, and software decoding, or CPU decoding.

See the reference video for details[^4].

# Visio

Download and activation: [Microsoft Visio Professional 2019_visio 2019-CSDN Blog](https://blog.csdn.net/milkhq/article/details/105714076)

# References

[^1]: [How to set the Edge search bar](https://answers.microsoft.com/zh-hans/microsoftedge/forum/all/edge使用必应搜/6637cc55-5366-4a01-adc3-fd5db4b666fd)
[^2]: [Tutorial: Beautifying Windows Terminal with Windows Terminal + oh-my-posh_Bilibili](https://www.bilibili.com/video/BV1Qa411T7Au/?spm_id_from=333.337.search-card.all.click&vd_source=617c4a2b4e326fc6b6269aada0d25986)
[^3]: [Open PowerShell with Administrator Privileges in Windows Terminal - Cyber-Cynic - cnblogs.com](https://www.cnblogs.com/talentzemin/p/15930400.html)
[^4]:[Watch TV and Slack Off! A Powerful PotPlayer Usage Guide and Tips_Bilibili](https://www.bilibili.com/video/BV1Tx4y1X7Fh/?spm_id_from=333.337.search-card.all.click&vd_source=617c4a2b4e326fc6b6269aada0d25986)
