# Technical Direction

## Product Positioning

`keysmith-mac` is intended to be a lightweight macOS keyboard utility focused on key event discovery and practical key remapping.

The initial goal is not to clone the full complexity of Karabiner-Elements. Instead, the project should first solve a narrower and more common problem: making third-party keyboard function-row and special keys behave correctly and persistently on macOS.

## Name Rationale

The name `keysmith-mac` combines three ideas:

- `key`: the core domain is keyboard keys, key codes, and key events.
- `smith`: the app reshapes and customizes keyboard behavior, like a craft tool rather than a generic settings panel.
- `mac`: the project is explicitly macOS-focused and does not imply cross-platform support.

This keeps the project independent from Karabiner-Elements while still communicating that it is a keyboard customization tool.

## Known Requirements

Based on the current product direction, the app should support:

- Identifying keyboard key codes and low-level key events.
- Displaying key information clearly enough for users to configure mappings.
- Modifying key behavior, especially for function-row and special keys on third-party keyboards.
- Keeping mappings effective after the main app exits.
- Optionally running a minimal background process to keep mappings active.
- Leaving enough architectural room for future keyboard-related features.

## Recommended Technology Stack

### macOS App

Use Swift and SwiftUI for the main application.

Reasons:

- Native macOS permission flows are easier to manage from Swift.
- SwiftUI is sufficient for a utility-style settings app.
- Native app distribution, menu bar integration, and helper management are simpler than with a web-based shell.

### Event Listening and Remapping

Use `CGEventTap` as the first implementation path.

It can listen to keyboard events and modify or suppress events before they reach normal applications, which is enough for the first version of simple key remapping.

Expected permission requirements:

- Accessibility permission.
- Input Monitoring permission, depending on the exact event access pattern and macOS version.

### Background Persistence

Use a lightweight helper process, tentatively named `keysmithd`, managed by a user-level `launchd` LaunchAgent.

This gives the project a clean persistence model:

- The main app is used for configuration, onboarding, permissions, and diagnostics.
- The helper applies mappings continuously in the background.
- Mappings remain active after the main app exits.
- The helper can be installed, restarted, or removed by the main app.

### Configuration Storage

Start with a local JSON or property list configuration file.

A simple file-based format is enough for early versions and keeps the helper independent from the app UI.

Possible location:

```text
~/Library/Application Support/Keysmith/config.json
```

The format should be explicit and versioned from the beginning so future migrations are manageable.

### Repository and Build Structure

A practical initial structure could be:

```text
keysmith-mac/
  README.md
  docs/
    technical-direction.md
  KeysmithApp/
    # SwiftUI app target
  KeysmithDaemon/
    # background helper / CLI target
  KeysmithCore/
    # shared key event, mapping, config logic
  packaging/
    # LaunchAgent plist, install scripts, release notes
```

The exact structure should be finalized after choosing whether to start with a Swift Package, Xcode project, or both.

## Recommended Architecture

### Main App

The app should own user-facing workflows:

- Permission onboarding.
- Live key event inspection.
- Mapping rule editing.
- Device selection and diagnostics.
- Helper installation, restart, and removal.
- Status display for whether remapping is currently active.

### Background Helper

The helper should stay small and boring.

Responsibilities:

- Load mapping configuration.
- Start the keyboard event tap.
- Apply enabled mapping rules.
- Watch for config changes or support explicit reloads.
- Log useful diagnostics without becoming a second application.

### Shared Core

Shared logic should live outside both the UI and helper:

- Key event models.
- Mapping rule models.
- Config parsing and validation.
- Device matching logic.
- Key code naming tables.

This avoids duplicating important behavior between the app and helper.

## First-Version Scope

The first real implementation should be intentionally narrow:

1. Display incoming key events, including key code, flags, event type, and available device information.
2. Support simple one-to-one key mappings.
3. Support device-scoped mappings, so external keyboards can be customized without affecting the built-in keyboard.
4. Persist mappings in a local config file.
5. Run mappings through a background helper after the main app exits.
6. Provide clear permission and helper status in the app.

## Features to Avoid in Version One

Avoid these until the core path is stable:

- Full Karabiner-style complex modification rules.
- Deep HID-driver-level remapping.
- Kernel extensions.
- Cloud sync.
- Scripting engines.
- Large profile systems.
- Cross-platform support.

These may be useful later, but they would make the first version much harder to finish.

## Future Expansion Ideas

After the first version is stable, possible additions include:

- Profiles for different keyboards or contexts.
- Import/export of mapping rules.
- More advanced modifier-key conditions.
- App-specific mappings.
- Menu bar quick toggles.
- Rule validation and conflict detection.
- Better human-readable names for unusual vendor keys.
- Diagnostics export for debugging keyboard issues.

## Open Technical Questions

These should be answered before implementation starts:

- How reliably can `CGEventTap` identify the physical source device for each event across common third-party keyboards?
- Which special keys appear as normal key events, and which appear as media/system events?
- Which mappings can be implemented purely with `CGEventTap`, and which require lower-level HID handling?
- What is the safest install/update/uninstall flow for the background helper?
- Should configuration live under the app group container, Application Support, or another location?
- Should the first build be a pure Swift Package, an Xcode project, or an Xcode project backed by Swift packages?

## Release Strategy

Do not create a release until the project has a working implementation that is useful to install or test.

Recommended release path:

- Use Conventional Commits for history readability and future automation.
- Keep `CHANGELOG.md` updated under an `[Unreleased]` section.
- Start with `v0.1.0` for the first experimental build.
- Use semantic versioning after releases begin.
- Create annotated tags for releases.
- Publish GitHub Releases from tags with notes copied from the matching changelog section.

Early release candidates can use suffixes such as `v0.1.0-alpha.1` if the app is installable but not stable.

## Suggested Next Step

Before writing implementation code, the project should define a small requirements document covering:

- Target keyboard examples.
- Target remapping examples.
- Minimum supported macOS version.
- Permission UX.
- Whether the app should live in the Dock, menu bar, or both.
- Whether helper persistence is mandatory for the first release.
