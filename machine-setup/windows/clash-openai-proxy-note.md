---
RolloutDate: 2026-03-21
---

# Codex app reconnect issue with windows clash

![codex reconnect](./resources/images/codex-reconnect.png)

> 2026-11-18 更新：下文是旧 Clash 配置的历史排障方案，相关维护脚本已停用，不要照此重新安装或运行。目前 `sg` 使用 Clash Verge 的 Rules Enhancement 维护明确的 OpenAI 代理规则，不依赖 `clash-fix-openai`。旧脚本的文件清理仍待完成。

## Issue Description

在 Windows 10 环境中使用 Codex 时，频繁出现 `reconnect` 之后才能成功 thinking

## Possible Cause

当前使用的 Clash 订阅规则里，存在：

```yaml
- GEOIP,CN,DIRECT
- MATCH,⚓️其他流量
```

这两条规则本身不能证明重连的原因。[Mihomo 的规则说明](https://wiki.metacubex.one/config/rules/)指出，规则按从上到下的顺序匹配；`GEOIP` 匹配的是目标 IP 的数据库归属，不是域名所属公司的国家。

一种需要验证的可能情况是：

1. 请求未被前面的域名规则接住，随后用于匹配的目标 IP 被 GeoIP 数据库归入 `CN`。
2. 如果命中 `GEOIP,CN,DIRECT`，该连接就会走直连；如果没有命中，则继续判断后面的规则。
3. 如果这条连接直连失败，可能导致应用重连。但节点链路、DNS、TLS 或服务端异常等也可能产生重连现象。

原文没有保留足以核实上述因果链的真实连接日志、解析 IP 和数据库查询结果，因此不能把它写成已经确认的根因。即使观察到 `GeoIP(CN)`，也不能单凭这一点区分 DNS 返回异常和 GeoIP 数据库归类问题。

## Resolution

在当前活动订阅文件中，把 OpenAI 相关域名规则放到 `GEOIP,CN,DIRECT` 前面。核心规则如下：

```yaml
- DOMAIN-SUFFIX,chatgpt.com,⚓️其他流量
- DOMAIN-SUFFIX,openai.com,⚓️其他流量
- DOMAIN-SUFFIX,oaistatic.com,⚓️其他流量
- DOMAIN-SUFFIX,oaiusercontent.com,⚓️其他流量
```

在规则模式下，只要 Clash 能识别连接的目标域名，且这些规则之前没有其他规则先命中，这些域名及其子域名就会优先走指定代理组。这是明确路由意图的做法，不是已经证实重连根因，也不能保证消除所有重连。

但是如果我们手动把订阅更新了，那么这些手工规则就会被覆盖。所以我们额外做了一个手动自动修复方案。

我们写了一个 ps scripts 放在：`C:\Users\Windows 10\.config\clash\codex-maintain-openai-rules.ps1`

作用：

1. 找到当前活动的 Clash profile
2. 自动检查 OpenAI 规则是否存在
3. 如果缺失，则在 `GEOIP,CN,DIRECT` 前重新插入
4. 生成当前运行用的 runtime 配置
5. 热重载 Clash

然后在 ps profile 中添加了这样子一条 powershell command

```powershell
# manually repair Clash OpenAI rules after updating subscriptions
function Fix-ClashOpenAIRules {
    powershell.exe -NoProfile -ExecutionPolicy Bypass -File "$env:USERPROFILE\.config\clash\codex-maintain-openai-rules.ps1"
}

Set-Alias -Name clash-fix-openai -Value Fix-ClashOpenAIRules
```

这样子我们，手动更新 clash 订阅之后，就可以使用命令 clash-fix-openai 把这些 rule 写进去了

## Troubleshoot

如果之后又出现 reconnect，如何判断 OpenAI 规则还在，以及脚本是否生效

按下面顺序检查：

1. Clash 是否已启动，且本地端口 `127.0.0.1:7890` 仍在监听
2. 当前 profile 中是否还保留 OpenAI 规则
3. 看 Clash 日志是否命中了 DomainSuffix 规则。

```powershell
$log = Get-ChildItem "$env:USERPROFILE\.config\clash\logs" |
  Sort-Object LastWriteTime -Descending |
  Select-Object -First 1

Select-String -Path $log.FullName -Pattern "chatgpt.com|chat.openai.com|DomainSuffix\(chatgpt.com\)|DomainSuffix\(openai.com\)|GeoIP\(CN\)"
```

下面只是日志格式示例，不是当时保留的故障证据：

```text
rule=DomainSuffix(chatgpt.com)
rule=DomainSuffix(openai.com)
```

说明 OpenAI 请求正在按我们加的规则走。

如果看到的是：

```text
rAddr=chatgpt.com:443 ... rule=GeoIP(CN) proxy=DIRECT
```

如果真实连接日志中同一条 OpenAI 请求出现了上述命中情况，只能先确认该连接被规则分到了直连。还需核对当时实际使用的目标 IP、DNS 结果和直连错误，才能继续判断原因。

## powershell scripts

主维护脚本 ps 代码

文件：`C:\Users\Windows 10\.config\clash\codex-maintain-openai-rules.ps1`

```powershell
$ErrorActionPreference = "Stop"

$clashRoot = Join-Path $env:USERPROFILE ".config\clash"
$profilesDir = Join-Path $clashRoot "profiles"
$generalConfig = Join-Path $clashRoot "config.yaml"
$profilesList = Join-Path $profilesDir "list.yml"
$runtimeConfig = Join-Path $profilesDir "codex-active-runtime.yml"
$utf8 = [System.Text.UTF8Encoding]::new($false)

$startMarker = "  # codex-openai-rules-start"
$endMarker = "  # codex-openai-rules-end"

function Get-GeneralValue {
    param(
        [string[]]$Lines,
        [string]$Key
    )

    foreach ($line in $Lines) {
        if ($line -match "^\s*$([regex]::Escape($Key)):\s*(.+?)\s*$") {
            return $Matches[1]
        }
    }

    return $null
}

function Get-MatchProxyName {
    param(
        [System.Collections.Generic.List[string]]$Lines
    )

    foreach ($line in $Lines) {
        if ($line -match '^\s*-\s*MATCH,(.+?)\s*$') {
            return $Matches[1]
        }
    }

    throw "Could not determine fallback proxy name from MATCH rule"
}

function Read-Utf8Text {
    param(
        [string]$Path
    )

    return [System.IO.File]::ReadAllText($Path, $utf8)
}

function Read-Utf8Lines {
    param(
        [string]$Path
    )

    return [System.IO.File]::ReadAllLines($Path, $utf8)
}

function Write-Utf8Lines {
    param(
        [string]$Path,
        [string[]]$Lines
    )

    [System.IO.File]::WriteAllLines($Path, $Lines, $utf8)
}

function Update-ProfileRules {
    param(
        [string]$Path
    )

    $lines = [System.Collections.Generic.List[string]]::new()
    foreach ($line in Read-Utf8Lines -Path $Path) {
        [void]$lines.Add($line)
    }

    $geoIndex = -1
    for ($i = 0; $i -lt $lines.Count; $i++) {
        if ($lines[$i] -match '^\s*-\s*GEOIP,CN,DIRECT\s*$') {
            $geoIndex = $i
            break
        }
    }

    if ($geoIndex -lt 0) {
        return $false
    }

    $startIndex = $lines.IndexOf($startMarker)
    if ($startIndex -ge 0) {
        $endIndex = $lines.IndexOf($endMarker)
        if ($endIndex -ge $startIndex) {
            $lines.RemoveRange($startIndex, $endIndex - $startIndex + 1)
            if ($startIndex -lt $geoIndex) {
                $geoIndex -= ($endIndex - $startIndex + 1)
            }
        }
    }

    $proxyName = Get-MatchProxyName -Lines $lines
    $managedBlock = @(
        $startMarker,
        "  - DOMAIN-SUFFIX,chatgpt.com,$proxyName",
        "  - DOMAIN-SUFFIX,openai.com,$proxyName",
        "  - DOMAIN-SUFFIX,oaistatic.com,$proxyName",
        "  - DOMAIN-SUFFIX,oaiusercontent.com,$proxyName",
        $endMarker
    )

    for ($i = 0; $i -lt $managedBlock.Count; $i++) {
        $lines.Insert($geoIndex + $i, $managedBlock[$i])
    }

    Write-Utf8Lines -Path $Path -Lines $lines
    return $true
}

function Get-ActiveProfilePath {
    param(
        [string]$ListPath,
        [string]$ProfilesRoot
    )

    $raw = Read-Utf8Text -Path $ListPath
    $indexMatch = [regex]::Match($raw, '(?m)^index:\s*(\d+)\s*$')
    if (-not $indexMatch.Success) {
        throw "Could not find active profile index in $ListPath"
    }

    $selectedIndex = [int]$indexMatch.Groups[1].Value
    $entries = [regex]::Matches($raw, '(?m)^\s*(?:-\s*)?time:\s*(.+?)\s*$') |
        ForEach-Object { $_.Groups[1].Value }

    if ($selectedIndex -ge $entries.Count) {
        throw "Active profile index $selectedIndex is out of range"
    }

    return Join-Path $ProfilesRoot $entries[$selectedIndex]
}

function Build-RuntimeConfig {
    param(
        [string]$SourcePath,
        [string]$DestinationPath,
        [string]$Controller,
        [string]$Secret
    )

    $lines = Read-Utf8Lines -Path $SourcePath
    $updated = foreach ($line in $lines) {
        if ($line -match '^log-level:') {
            "log-level: info"
        }
        elseif ($line -match '^external-controller:') {
            "external-controller: $Controller"
        }
        elseif ($line -match '^secret:') {
            "secret: $Secret"
        }
        else {
            $line
        }
    }

    Write-Utf8Lines -Path $DestinationPath -Lines $updated
}

function Reload-ActiveConfig {
    param(
        [string]$Controller,
        [string]$Secret,
        [string]$ConfigPath
    )

    $body = @{ path = $ConfigPath } | ConvertTo-Json
    Invoke-RestMethod `
        -Headers @{ Authorization = "Bearer $Secret" } `
        -Uri "http://$Controller/configs?force=true" `
        -Method Put `
        -ContentType "application/json" `
        -Body $body | Out-Null
}

if (-not (Test-Path $generalConfig)) {
    throw "Missing Clash general config: $generalConfig"
}

if (-not (Test-Path $profilesList)) {
    throw "Missing Clash profile list: $profilesList"
}

$generalLines = Read-Utf8Lines -Path $generalConfig
$controller = Get-GeneralValue -Lines $generalLines -Key "external-controller"
$secret = Get-GeneralValue -Lines $generalLines -Key "secret"

if (-not $controller) {
    throw "Missing external-controller in $generalConfig"
}

if ($null -eq $secret) {
    $secret = ""
}

$profileFiles = Get-ChildItem $profilesDir -Filter *.yml |
    Where-Object {
        $_.Name -notin @("list.yml", "codex-active-runtime.yml") -and
        $_.Name -notlike "*.codex-merged.yml"
    }

foreach ($profile in $profileFiles) {
    $raw = Read-Utf8Text -Path $profile.FullName
    if ($raw -match '(?m)^rules:\s*$' -and $raw -match '(?m)^\s*-\s*GEOIP,CN,DIRECT\s*$') {
        Update-ProfileRules -Path $profile.FullName | Out-Null
    }
}

$activeProfile = Get-ActiveProfilePath -ListPath $profilesList -ProfilesRoot $profilesDir
Build-RuntimeConfig -SourcePath $activeProfile -DestinationPath $runtimeConfig -Controller $controller -Secret $secret
Reload-ActiveConfig -Controller $controller -Secret $secret -ConfigPath $runtimeConfig
```
