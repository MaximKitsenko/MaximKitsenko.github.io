---
layout: post
title: Git useful commands
---

For those who uses Git (including me) i started to collect useful commands. In this post i plan to collect some commands that i use often.


**Stash** the untracked files as well as the tracked ones (by default, git stash will only store files that are already in the index):

```git stash -u```


Selectively **checkout** file from stash:

```git checkout stash@{0} -- C:/projects/skuvault/src/Infrastructure/StorageSettingsProvider.cs```


Create **patch** from some commit (that commit excluded). (commits count +1 files will be created):
```git format-patch bd5f6f16ffab0b88ca58833bb97aadefdf90c083 >devvm.patch```


Apply **patch** (will search all patches and apply commits from them)
```git am *.patch```

Preview **patch**:

```git apply --stat fix_empty_poster.patch```


Apply **patch**:

```git apply --exclude=*.csv  c:/projects/ebayAccess/patch.diff```


**Find** all `.cs` files from current dir containing specified string:

```git grep 'GetProductsAsync' -- '*.cs'```


**Find** all commits containing specified string in file:

```git log -S "magentoService.GetProductsAsync" --oneline```


**Alias** adding:

```git config --global alias.co checkout``` 