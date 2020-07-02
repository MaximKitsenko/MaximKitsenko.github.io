---
layout: post
title: Cheat sheet - nunit-console useful commands
---

## What is nunit-console

**Nunit-Console** - a tool that allows you to run unit tests from the command line. Often it is used to run tests on the **CI / CD** server, but I prefer to use it on my local machine instead of GUI tools. Since I often use nunit-console, I decided to create a cheat sheet with them. I will try to add new commands as soon as they appear in my set of frequently used commands.

## Nunit-console commands

All examples bellow are used from nunit console path, on my machine it is `c:\Program Files (x86)\NUnit.org\nunit-console>`

### Run all tests which contains 'GivenTh' in their name

```pwsh
nunit3-console.exe "C:\max-projects\filmtrack\avails\FilmTrack.Avails.Core.Tests\bin\Debug\FilmTrack.Avails.Core.Tests.dll" --where "test =~ GivenTh"
```

### Save tests run result to a file

```pwsh
nunit3-console.exe "C:\max-projects\filmtrack\avails\FilmTrack.Avails.Core.Tests\bin\Debug\FilmTrack.Avails.Core.Tests.dll" --out "C:\max-projects\filmtrack\TestsResult.txt"
```
