# Small hack for fast copying boilerplate code (when codegenerator is too much)

## Problem of copying boilerplate code

Have you ever faced with the situation when you need to implement some boilerplate code from time to time but for some reason code generator is not suitable for this task? In this article i'll show how we rapidly found a good balance, and solved this problem with help of a tiny script ( handmade alternative for **T4** ). You can skip this text, here the [script](https://github.com/MaximKitsenko/bonanza-scripts/blob/master/scripts/replace-in-files.ps1) which helped us.

In the current project developers often face with the situation when there is need to copy boilerplate code. For example we have *TerritoriesRepository.cs*, *ITerritoriesRepository.cs*, *Territory.cs* and we want to add *SalesRepository.cs*, *ISalesRepository.cs*, *Sale.cs*. For all files the code structure will be the same except few places where variables/methods/parameters names have different prefix. For example look to the following code:

```C#
// Territory.cs
public class Territory
{
    ...
}
// ITerritoriesRepository.cs
Interface ITerritoriesRepository
{
   Territory GetyId(Guid territory);
}

// TerritoriesRepository.cs
public class TerritoriesRepository: ITerritoriesRepository
{
   public Territory GetyId(Guid territory)
   {
       ...
       return territories;
   }
}
... other files
```

```C#
// Sale.cs
public class Sale
{
    ...
}
// ISalesRepository.cs
Interface ISalesRepository
{
   Sale GetyId(Guid sale);
}
// SalesRepository.cs
public class SalesRepository: ISalesRepository
{
   public Sale GetyId(Guid sale)
   {
       ...
       return sales;
   }
   ...
}
... other files
```

Naturally arising desire is to copy *TerritoriesRepository.cs*, *ITerritoriesRepository.cs*, *Territory.cs* to *SalesRepository.cs*, *ISalesRepository.cs*, *Sale.cs*, then casesensitive "ReplaceAll" 4 times on each of all 3 files:

- "Territory" to "Sale";
- "territory" to "sale";
- "Territories" to "Sales";
- "territories" to "sales".

For simplicity i described only 3 files, in reality we have 10 similar files for one domain entity. If you want to copy-paste 3 domain entities it may take some significant time even if you use hotkeys. This amount of time may be significant but still not enough to justify creating your own codegenerator. We want to automate creation of such boilerplate code ( **DRY**! ). These files shouldn't be changed, they are part proof concept project, so fullvalue codegenerator looks like overcomplicated solution. The problem is - we need light weight toolkit for copying boilerplate code.

## Solution

The first idea was to use **Text Template Transformation Toolkit** (T4). There are a lot of T4 toolkits. But a lot of them looks bulky for our goals. We decided to create tiny tool, which:

- can be written in 15 minutes;
- it's ok to make few (but not all) operations manually;
- doesn't require heavy **IDE**, can be used in simple text editor;
- easy to use and maintain ( **KISS**! ).

Implementation of this idea satisfying requirements above is a small [script](https://github.com/MaximKitsenko/bonanza-scripts/blob/master/scripts/replace-in-files.ps1). We just took the idea of copying/replacing and automated these *ReplaceAll* operations. What we do:

- we manually copy set of files with boilerplate code;
- provide *find-replace* words and files paths to the script;
- run script.

This solution doesn't automate all operations. But sicne we implemented it in 15 minutes in addition source code shouldn't change often, so for us it looks like a good balance between time for building this script and full value codegenerating.