---
title: ePOwerShell
author: Michael Arrañaga
layout: post
tags:
    - PowerShell
    - PoSh
    - PowerShell Gallery
    - ePO
    - McAfee ePolicy Orchestrator
---

[![Build status](https://ci.appveyor.com/api/projects/status/t3kx0sy41ouw7cry?svg=true)](https://ci.appveyor.com/project/UNT-CAS/ePOwerShell)
[![codecov](https://codecov.io/gh/UNT-CAS/ePOwerShell/branch/master/graph/badge.svg)](https://codecov.io/gh/UNT-CAS/ePOwerShell)
[![version](https://img.shields.io/powershellgallery/v/ePOwerShell.svg)](https://www.powershellgallery.com/packages/ePOwerShell)
[![downloads](https://img.shields.io/powershellgallery/dt/ePOwerShell.svg?label=downloads)](https://www.powershellgallery.com/packages/ePOwerShell)

A lot of our day to day business requires working with McAfee's ePolicy Orchestrator (aka, ePO). We're consistently adding and removing tags in ePO, querying systems for the system stored there. To make things easier for our techs, we wrote a quick and useful module to work with ePO systems.

## Quick Setup

1. Install ePOwerShell: `Install-Module ePOwerShell`
2. Import ePOwerShell: `Import-Module ePOwerShell`
3. Configure the server settings: `Set-ePOwerShellServer`

## Important Notes

Before you're able to use ePOwerShell, you must first configure the server settings. For the moment, this does require you to save credentials somewhere. We've provided 3 methods to do so:

1. By typing in all parameters each time

    ```powershell
    Set-ePOwerShellServer -Server 'https://your-epo-server.domain.com' -Port 1234 -Credentials (Get-Credential)
    ```

2. By creating and storing a json in your environment variables as `$env:ePOwerShell`

    ```powershell
    $Credentials = (Get-Credential); [Environment]::SetEnvironmentVariable("ePOwerShell", (@{Server = (Read-Host -Prompt "Server");Port = (Read-Host -Prompt "Port");Username = $Credentials.Username;Password = ($Credentials.Password | ConvertFrom-SecureString);} | ConvertTo-Json -Compress), "User")
    ```

3. By creating and storing a json file on your system, and setting `$env:ePOwerShell` as the filepath to said file.

    ```powershell
    $Credentials = (Get-Credential); $FilePath = (Read-Host -Prompt "Filepath"); @{Server = (Read-Host -Prompt "Server");Port = (Read-Host -Prompt "Port");Username = $Credentials.Username;Password = ($Credentials.Password | ConvertFrom-SecureString);} | ConvertTo-Json | Out-File $FilePath; [Environment]::SetEnvironmentVariable("ePOwerShell", $FilePath, "User")
    ```

## Useful features

* [Add](https://github.com/UNT-CAS/ePOwerShell/blob/master/ePOwerShell/Public/Set-ePOwerShellTag.ps1) and [Remove](https://github.com/UNT-CAS/ePOwerShell/blob/master/ePOwerShell/Public/Clear-ePOwerShellTag.ps1) ePO tags
* [Searching for computers](https://github.com/UNT-CAS/ePOwerShell/blob/master/ePOwerShell/Public/Find-ePOwerShellComputerSystem.ps1) based off:
    * `Hostname` (Default)
    * `MAC Address`
    * `IP Address`
    * Applied `Tags`
    * `Username`
    * `All` systems in ePO (Careful, this will take a while)
* [Get MNE Recovery Keys](https://github.com/UNT-CAS/ePOwerShell/blob/master/ePOwerShell/Public/Get-ePOwerShellMneRecoveryKey.ps1)

## Examples

[All examples](https://github.com/UNT-CAS/ePOwerShell/tree/master/Examples)

### Find a computer via Hostname

```powershell
Find-ePOwerShellComputerSystem 'Computer1'
```

### Remove a tag

```powershell
Clear-ePOwerShellTag 'Computer1' 'Tag1'
```

### Apply a tag

```powershell
Set-ePOwerShellTag 'Computer1' 'Tag1'
```

### Find MNE Recovery Key

```powershell
Get-ePOwerShellMneRecoveryKey -ComputerName $env:ComputerName
```

## In the works

* Add more ePO functions
* Change credntials to use a better solution, such as:
    * [Better Credentials](https://github.com/Jaykul/BetterCredentials)
    * [ProtectData](https://github.com/dlwyatt/ProtectedData)

## What I learned

You should learn something new every day, and working on ePOwerShell definitely introduced me to a bunch of new concepts and ideas.

### Pester tests

These take forever. Like, forever forever.

Each function requires a Pester test to confirm that it's working. Each test requires examples for different use cases. This can quickly add up to dozens of files.

### Pull requests and AppVeyor

Working on a project that's currently in a pull request does not pull secure environment values. I didn't realize this. Not knowing this resulted in far too many commits to Git than I'd care to admin trying to get Appveyor to acknowledge an environment variable. 

### Sorting a `PSCustomObject`

Sorting values from a `PSCustomObject` is extremely annoying and difficult (still a work in progress). By default, when searching for a computer system in ePO, it returns a Json with names such as `EPOComputerSystem.ComputerName`, or `EPOComputerSystem.AgentGUID`. Since that was kinda ugly, I filtered off the `EPOComputerSystem` from the name. I also wanted to sort the items in each object by their name. Unfortunately, I haven't been successful with that last part.

## Final thoughts

This was definitely a fun project (albiet slightly stressful). I'm a long way from finishing it, but will definitely get to it... soon. Until then, what we have so far will definitely help my collegues and me with our day to day processes!