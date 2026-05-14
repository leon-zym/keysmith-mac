# keysmith-mac

A lightweight macOS keyboard utility for discovering key events and applying practical persistent key remapping.

The project is inspired by the practical keyboard customization needs commonly solved with Karabiner-Elements, but the initial goal is intentionally narrower: make third-party keyboard function-row and special keys easier to identify, remap, and keep active on macOS.

## Status

Project planning stage. No implementation has started yet.

## Initial Goals

- Identify keyboard key codes and key events on macOS.
- Support simple persistent key behavior remapping.
- Focus first on third-party keyboard function-row and special keys.
- Keep mappings active after the main app exits, likely through a minimal background helper.
- Preserve room for future keyboard-related features.

## Technical Direction

See [`docs/technical-direction.md`](docs/technical-direction.md) for the current recommended technology stack, architecture, first-version scope, open technical questions, and release strategy.

## Changelog

See [`CHANGELOG.md`](CHANGELOG.md) for unreleased changes and the planned release process.

## Next Step

Define concrete product requirements before implementation begins, including target keyboards, example mappings, macOS version support, permission UX, and helper persistence behavior.
