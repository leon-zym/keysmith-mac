# Changelog

All notable changes to this project will be documented in this file.

This project follows a human-readable changelog style inspired by [Keep a Changelog](https://keepachangelog.com/), and will use semantic versioning once the first release is created.

## [Unreleased]

### Added

- Created the initial project shell.
- Documented the recommended technical direction, architecture, first-version scope, and open technical questions.
- Added release planning guidance for future tagged versions.

## Release Process

Releases should start after the project has a working implementation worth distributing or testing.

Recommended process:

1. Decide the version number using semantic versioning.
2. Move relevant entries from `[Unreleased]` into a dated release section, such as `## [0.1.0] - 2026-05-14`.
3. Commit the changelog update with a `docs:` or `chore:` commit.
4. Create an annotated Git tag, for example `git tag -a v0.1.0 -m "Release v0.1.0"`.
5. Push the tag with `git push origin v0.1.0`.
6. Create a GitHub Release from the tag and paste the matching changelog section as release notes.

For early experimental builds, start with `v0.1.0` rather than `v1.0.0`.
