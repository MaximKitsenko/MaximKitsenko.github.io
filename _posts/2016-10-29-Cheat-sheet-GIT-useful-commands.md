---
layout: post
title: Cheat sheet - Git useful commands
---

For those who uses Git (including me) i started to collect useful commands. In this post i plan to collect some commands that i use often.

## Stash

**Stash** the untracked files as well as the tracked ones (by default, git stash will only store files that are already in the index):

```git stash -u```

### Stash specific file

Keep in mind index should be clean, otherwise files added to index will be added.

```sh
git stash push -m projectAssignment FilmTrack.Avails.Core.Tests/OdataWebApiTests/ContractCardProjectAssignmentsControllerBulkRemoveProjectsTests.cs
```

## Checkout

Selectively **checkout** file from stash:

```git checkout stash@{0} -- C:/projects/skuvault/src/Infrastructure/StorageSettingsProvider.cs```

## Patch

Create **patch** from some commit (that commit excluded). (commits count +1 files will be created):

```git format-patch bd5f6f16ffab0b88ca58833bb97aadefdf90c083 >devvm.patch```

Apply **patch** (will search all patches and apply commits from them)

```git am *.patch```

Preview **patch**:

```git apply --stat fix_empty_poster.patch```

Apply **patch** (Don’t apply changes to files matching the given path pattern) :

```git apply --exclude=*.csv  c:/projects/ebayAccess/patch.diff```

## Search

**Find** all `.cs` files from current dir containing specified string:

```git grep 'GetProductsAsync' -- '*.cs'```

**Find** all commits containing specified string in file:

```git log -S "magentoService.GetProductsAsync" --oneline```

```git log -S ToObservable --source --all```

## Alias

**Alias** adding:

```git config --global alias.co checkout```

## Renaming

**Rename** remote branch:

```bash
git push origin :bug-fix/one-custom-section bug-fix/PB-426-one-custom-section
```

```
 - [deleted]               bug-fix/one-custom-section
 * [new branch]            bug-fix/PB-426-Remove-duplicate-custom-sections-from-solution -> bug-fix/PB-426-Remove-duplicate-custom-sections-from-solution
```
