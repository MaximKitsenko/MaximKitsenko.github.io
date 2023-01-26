
Few days ago, when i was debugging our application ( which was written with .net), i faced with issue, when the bug was hidden in the nuget package. It was required to debug the code inside the nuget package. In this post i will share my experience with debugging the code from the nugget package.

The first thing that i did - i tried to list all options which are possible to do in this situation:

1) remove nuget packages, add existing projects to the solution instead (fortunatelly these nuget packages were developed in our company and ia have access to source codes);
2) disassembly dll's from nuget packages;
3) generate pdb files some how.

## 1. Replace nuget packages with .csproj

At first glance this way is the simples one and i tried to use it. But i faced with a lot of problems with dependencies and dll versions. Since the project is huge, it uses a lot of dependencies, and it was difficult to find the correct version (commit) of project to add and refference on. After few hours of solving references issues i decided to use another way.


## 2. Disassembly dll's from nuget packages
This way was easy to use, but result may be not so good as you whish - dissassembled code may be no very readable. I decided to use this was as the last chance

## 3. Generate pdb files some how (that was the solution)
The main idea of this approach is to create the same nuget packages but with some additional information (pdb files) with help of which we can debug these nuget packages.

The first step is - choose correct commit in the nuget project repository, which correcponds to the required version of nuget package. I found commit hash in the .dll properties by Right click on dll -> Details (thanks to our CI/CD). After that we need to checkout required commit in the repository of this nuget package.

The next step is to create .nuspec file in .csproj folder, and add dependencies into this .nuspec:

```
<?xml version="1.0" encoding="utf-8"?>
<package xmlns="http://schemas.microsoft.com/packaging/2013/05/nuspec.xsd">
  <metadata>
    <id>ClearReports.Implementation</id>
    <version>0.23.0</version>
    <authors>Inc.</authors>
    <description>some description</description>
    <copyright>Inc. 2001 - 2022</copyright>
    <repository url="https://companyname.net/ClearReports.git" />
    <dependencies>
      <group targetFramework=".NETFramework4.8">
        <dependency id="CompanyName.ClearReports.UpgradeHandler.BuildTools" version="0.16.0" exclude="Build,Analyzers" />
      </group>
    </dependencies>
    <frameworkAssemblies>
      <frameworkAssembly assemblyName="System.IO.Compression" targetFramework=".NETFramework4.8" />
      <frameworkAssembly assemblyName="System.IO.Compression.FileSystem" targetFramework=".NETFramework4.8" />
      <frameworkAssembly assemblyName="System.IO" targetFramework=".NETFramework4.8" />
    </frameworkAssemblies>
  </metadata>
</package>
```

After that we need to build nuget packages with the following command:

```
nuget pack <project-name>.csproj
```

When the project is built we should have `.dll` and `.pdb`. Replace all target dlls in the solution to new dlls that we just built and place pdb next to them. That's all we a ready to debug. Next time when you need to step into the code from the nuget package, you can use `step into` (if you use VisualStudio).