---
layout: post
title: Git useful commands
---

For those who uses Git (including me) i started to collect useful commands. In this post i plan to collect some commands that i use often.


**Stash** the untracked files as well as the tracked ones (by default, git stash will only store files that are already in the index):

```$ git stash -u```


Selectively **pull** file from stash:

```$ git checkout stash@{0} -- C:/projects/skuvault/src/Infrastructure/StorageSettingsProvider.cs```


Preview **patch**:

```$ git apply --stat fix_empty_poster.patch```


Apply **patch**:

```$ git apply --exclude=*.csv  c:/projects/ebayAccess/patch.diff```


**Find** all `.cs` files from current dir containing specified string:

```$ git grep 'GetProductsAsync' -- '*.cs'```


**Find** all commits containing specified string in file:

```$ git log -S "magentoService.GetProductsAsync" --oneline```
