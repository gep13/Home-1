# Cake-Contrib Guidelines & Best Practices

This document provides some guidelines and best practices for writing Cake addins hosted within the cake-contrib GitHub Organization (however, these guidelines can also be applied to addin's hosted outside the Orgnanization).

All known addins are audited twice daily to ensure compliance, and a summary report, as well as a detailed report, are generated at the conclusion of each audit.
As a Cake user you can use the [summary audit report](Audit.md) to verify if the addins you are using are compatible with the version of Cake you are using. 
As an addin author you should use the [detailed audit report](Audit.xlsx) to verify that you are complying with all recommendations.

## Naming Convention

Your addin should be named `Cake.xxx` where xxx is a meaningful and unique name that describes what your addin does. We strongly encourage you to be consistent and use the same name for the GitHub repo, the C# solution name, the assembly generated from your project and the NuGet package. The `Cake.` prefix in the naming convention is particularly important for the NuGet package and the assembly in your package, because it's used by the automated [AddinDiscoverer](https://github.com/cake-contrib/Cake.AddinDiscoverer) to identify addins for Cake.

For example, `Cake.Email` is the name that clearly identifies the addin for Cake that allows emails to be sent from your build script. This name is used in the [GitHub repo](https://github.com/cake-contrib/Cake.Email), it's the name of the [solution file](https://github.com/cake-contrib/Cake.Email/blob/develop/Source/Cake.Email.sln), it's the name of the [project file](https://github.com/cake-contrib/Cake.Email/blob/develop/Source/Cake.Email/Cake.Email.csproj), the name of the [generated assembly](https://github.com/cake-contrib/Cake.Email/blob/develop/Source/Cake.Email/Cake.Email.csproj#L10) and finally, it's also the name of the [NuGet package](https://www.nuget.org/packages/Cake.Email/).

## NuGet Package Icon

Addins should use the Cake Contrib icon rather than the Cake icon. You can do so by adding the following line in your addin's `.csproj`:

```xml
<PackageIconUrl>https://cdn.rawgit.com/cake-contrib/graphics/a5cf0f881c390650144b2243ae551d5b9f836196/png/cake-contrib-medium.png</PackageIconUrl>
```

Like in this [example](https://github.com/cake-contrib/Cake.Email/blob/develop/Source/Cake.Email/Cake.Email.csproj#L18).

If you are still using a `.nuspec` file rather than the newer `.csproj` format, the line should look like this:

```xml
<metadata>
    <iconUrl>https://cdn.rawgit.com/cake-contrib/graphics/a5cf0f881c390650144b2243ae551d5b9f836196/png/cake-contrib-medium.png</iconUrl>
</metadata>
```

Please note that the source for the icon can be found [here](https://github.com/cake-contrib/graphics).


## Build Infrastructure

Addins which are part of the cake-contrib Organization should use the [Cake.Recipe] scripts for their build process.

## Supported Cake Version

To have the best support for different versions of Cake, the addin should currently be built against Cake version 0.28.0,
or, if a specific newer functionality is required, the lowest version providing the specific functionality. Please note 
that addins built against newer versions of Cake might not be compatible with previous versions of Cake and vice-versa,
addins built against older versions might not be compatible with future versions of Cake (this is especially true when a
future version of Cake introduces breaking changes).

It is incumbent on addin authors to upgrade their references and publish new version of their NuGet packages when a new 
version of Cake with breaking changes becomes available.

## Framework

As of Cake 0.26.0, addins should only target `netstandard2.0`. Addins *can* multi-target `net46` (or even `net461`) and
`netstandard2.0` if you have a compelling reason, but for the vast majority of addins `netstandard2.0` is sufficient. 
The reason why we recommend netstandard2.0 is that this framework is compatible with both cake and cake.coreclr.

Previously, before moving to dotnet core 2, the only way to support both cake and cake.coreclr was to multitarget (net46 and netstandard1.6). 
Nowadays there’s (in most cases) no real need to multitarget, but many addins still do (mainly because they did it earlier).

## Cake.Core / Cake.Common references

Those references are being implicitly added by the Cake engine. Thus there is no need to add them as dependencies
inside the nuspec file. This is not too hard to accomplish of you write your own nuspec file. But if you are using
`dotnet.exe pack` to create the nuget package you need to explicitly mark those references as private assets inside
the new csproj format.

```xml
<ItemGroup>
  <PackageReference Include="Cake.Core" Version="0.28.0" PrivateAssets="All" />
  <PackageReference Include="Cake.Common" Version="0.28.0" PrivateAssets="All" />
</ItemGroup>
```

## Documentation

Addins should follow the [Cake Documentation Guidelines].

## Addin documentation

The preferred tool for creating documentation for the addin is [Wyam].
The [Cake.Recipe] scripts support building [Wyam] projects and the created website can be published to
`https://cake-contrib.github.io/Cake.AddinName/` using a `gh-pages` branch.

For details see [How to create gh-pages branch].

## Deprecating an addin

From time to time, a previously published addin  needs to be deprecated. This could be for a number of reasons:

- It was created in error.
- It has been superseded by another package.
- It is an older package that no longer follows the guidelines.
- Its package id has been changed to something that better fits with the package naming guidelines.

All versions of this package could simply be unlisted from nuget.org, meaning that they could no longer be found, however, this solution is not ideal. Any build script referencing this package without a specific version would no longer works which is far from ideal. When a package needs to be deprecated, it needs to be handled in such a way that existing users will continue to be able to use the old package id, but take advantage of the replacement package, if there is one.

In order to deprecate an addin, the following steps should be followed:

- Create a new version of the deprecated addin package.
- Prepend `[DEPRECATED]` to the title of the package (e.g. "[DEPRECATED] My Cake addin")
- Update the package description: Why is the package being deprecated?
- Publish this new version of the package to nuget.org

## Further reading

- [Cake.Recipe](https://github.com/cake-contrib/Cake.Recipe)
- [Wyam](https://wyam.io/)
- [Cake Documentation Guidelines](https://cakebuild.net/docs/contributing/documentation)
- [How to create gh-pages branch](https://www.gep13.co.uk/blog/how-to-create-gh-pages-branch)
