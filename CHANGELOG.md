# Changelog
All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.0] - 2022-02-09

### Added

- Auto-detection of the TLD install directory for Steam or Epic.
- Automatic configuration of reference paths (`MelonLoader`,
  `MelonLoader\Managed` and `Mods` directories) and references for the project.
- Custom targets for the build process:
  - AssetBundle files building
  - ModComponent directory zipping
  - Mod DLL and ModComponent files copying to the TLD Mods folder.
- Custom run target for Visual Studio to start the game directly from the IDE
  (Steam or Epic only).
