# Spec Delta

## Purpose

Lets users see which version of the app they are running, so they can include it when reporting a problem.

## ADDED Requirements

### Requirement: Footer on the main page
The system SHALL render a `<footer>` element at the bottom of the main page.

#### Scenario: Footer present
- **WHEN** the main page is rendered
- **THEN** a `<footer>` element exists as the last content element of the page

### Requirement: Footer shows the package version
The footer SHALL display the text "Version " followed by the `version` field of `package.json`.

#### Scenario: Current version displayed
- **WHEN** `package.json` has version `0.0.0` and the main page is rendered
- **THEN** the footer text is "Version 0.0.0"

### Requirement: Version is resolved at build time
The displayed version SHALL be read from `package.json` at build time, so that changing it and rebuilding changes the footer text with no other code change.

#### Scenario: Version bump and rebuild
- **WHEN** the `version` in `package.json` is changed to `1.2.3` and the app is rebuilt
- **THEN** the footer text is "Version 1.2.3" without any other source change

#### Scenario: No hard-coded version in components
- **WHEN** the source of the components is inspected
- **THEN** it contains no literal version number for the footer

### Requirement: Footer is readable and build stays healthy
The footer SHALL be legible against the page background using minimal styling, and the project SHALL continue to build and lint cleanly.

#### Scenario: Build and lint
- **WHEN** `pnpm build` and `pnpm lint` are run
- **THEN** both succeed

#### Scenario: Readable footer
- **WHEN** the footer is displayed in light or dark color scheme
- **THEN** its text is visible and not obscured by other content
