# The Long Dark MSBuild extensions

A NuGet package providing MSBuild extensions for The Long Dark modders.

This project aims to make it easier to build and test The Long Dark mods. It
provides the following features:
- [Auto-detection of the TLD install directory](#TLDDir).
- Automatic configuration of [reference paths and references](#References) for
  the project.
- Custom targets for the [build process](#Build):
  - Build AssetBundle files
  - Zip the ModComponent directory
  - Copy mod DLL and ModComponent files to the TLD Mods for faster
    debugging and tests
- [Custom run target](#Run) for Visual Studio to start the game directly from the IDE.

See [CHANGELOG.md](./CHANGELOG.md) for notable changes between versions.

Check out [TLDModTemplate](https://github.com/Kardyne/TLDModTemplate) for a
mod template using this package.

## Getting started

Right click on your project in Visual Studio and select the
`Manage NuGet packages...` menu item. From there, search for
`Kardyne.TLDBuildExtensions` and you should find the package.

Or add this to your `.csproj` file, anywhere after the initial import:

```xml
<ItemGroup>
  <PackageReference Include="Kardyne.TLDBuildExtensions" Version="1.0.0"/>
</ItemGroup>
```

You may need to reload the solution for the changes to take effect.

## Usage

### Customization

To customize project parameters such as the path to the TLD directory or the
path to the Unity Editor, create a `Directory.Build.props` file at the root of
your project or solution and add:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Project xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
	<PropertyGroup>
		<TLDDir></TLDDir>
		<UnityEditorPath></UnityEditorPath>
		<UnityProjectRelativeDir></UnityProjectRelativeDir>
		<ModComponentRelativeDir></ModComponentRelativeDir>
	</PropertyGroup>
</Project>
```

Below is the description for the parameters with their default values:

| Parameter               | Default value                         | Description                                                      |
| ----------------------- | ------------------------------------- | ---------------------------------------------------------------- |
| TLDDir                  | See [TLDDir](#TLDDir)                 | Absolute path to the TLD root directory                          |
| UnityEditorPath         | `ProgramFiles\Unity\Editor\Unity.exe` | Absolute path to the Unity Editor executable                     |
| UnityProjectRelativeDir | Unity\                                | Relative path (from project root) to the Unity project directory |
| ModComponentRelativeDir | Distributable\                        | Relative path (from project root) to the ModComponent directory  |

Note: for default values, `ProgramFiles` depends on the environment variable
of the same name and is set by the system. It includes the correct drive
letter.

#### TLDDir

The default value is set to the Steam folder if found in the default library.
The location of the Steam install is saved in the Windows registry which
contains the default Steam library folder. If your Steam TLD install is
not located in this library, see [Customization](Customization).

By reading and parsing the `UnrealEngineLauncher` library file (usually at
`ProgramData\Epic\UnrealEngineLauncher\LauncherInstalled.dat`), we can retrieve
the folders of games installed through EGL. If the Steam install has not been
found, it will use the Epic folder if it exists.

### References

If the TLDDir property is set and the directory exists, three sub-directories
are added as reference paths to the project:
- `TLDDir\MelonLoader`
- `TLDDir\MelonLoader\Managed`
- `TLDDir\Mods`

Then, the following references are added:
- `Assembly-CSharp`
- `Il2Cppmscorlib`
- `Il2CppSystem`
- `MelonLoader`
- `ModComponent`
- `ModSettings`
- `UnhollowerBaseLib`
- `UnityEngine`
- `UnityEngine.CoreModule`

### Build

In Visual Studio, running the `Build` command will trigger the extensions
included in this template:
- Build AssetBundle files
- Copy these files to the ModComponent directory
- Zip the directory to create the `.modcomponent` file
- Copy the ModComponent file and the and the mod DLL to the TLD Mods directory
  when in `Debug` configuration.

The additional components are triggered only as needed by following
[Incremental builds](https://docs.microsoft.com/en-us/visualstudio/msbuild/incremental-builds)
guidelines.

### Run

If the TLD install directory was found automatically (see [TLDDir](#TLDDir)),
a custom run target is configured depending on which install was found.
This makes it possible to [build](#Build) the mod, have the custom targets
copy the resulting files to the TLD mods directory and then launch the game
directly from the IDE using the `Start` button in Visual Studio.

## Known issues and limitations

### Auto-detection

Auto-detection of TLD directory is only reliable for the Epic Games Launcher
for now. You can override values as explained in
[Customization](#customization) if it does not work. You may need to do
this _before_ opening the solution in Visual Studio or the project may not
open at all.

Auto-detection of the Unity Editor executable may run into the same issues.
