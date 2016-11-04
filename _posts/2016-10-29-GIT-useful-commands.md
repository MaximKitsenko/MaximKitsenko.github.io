---
layout: post
title: Git useful commands
---

For those who uses Git (including me) i started to collect useful commands. In this post i plan to collect some commands that i use often.

**Selectively pull file from stash**
```git checkout stash@{0} -- C:/projects/skuvault/src/Infrastructure/StorageSettingsProvider.cs```

**Preview patch**
```git apply --stat fix_empty_poster.patch```

**Apply patch**
```$ git apply --exclude=*.csv  c:/projects/ebayAccess/patch.diff```
