# GitHub Pages Publishing Specification

## ADDED Requirements

### Requirement: GitHub Pages Configuration
The repository SHALL be configured to serve chart packages via GitHub Pages from the `docs/` directory.

#### Scenario: Pages enabled from docs directory
- **WHEN** GitHub Pages is configured
- **THEN** it MUST serve content from `docs/` directory on `main` branch
- **AND** it MUST be accessible at `https://binhnguyenduc.github.io/helm-charts/`

#### Scenario: HTTPS enforcement
- **WHEN** chart repository is accessed
- **THEN** GitHub Pages MUST serve content over HTTPS
- **AND** HTTP requests MUST be redirected to HTTPS

### Requirement: Repository Landing Page
The repository SHALL provide a landing page explaining the Helm chart repository purpose and usage.

#### Scenario: HTML landing page
- **WHEN** users visit `https://binhnguyenduc.github.io/helm-charts/`
- **THEN** they MUST see a `docs/index.html` page
- **AND** it MUST contain instructions for adding the chart repository
- **AND** it MUST list available charts or link to the chart index

#### Scenario: Human-readable documentation
- **WHEN** users access the landing page
- **THEN** it MUST explain the command: `helm repo add [name] https://binhnguyenduc.github.io/helm-charts/`
- **AND** it MUST provide example usage for installing charts

### Requirement: Chart Repository Index
The repository SHALL maintain a Helm-compatible chart index accessible via GitHub Pages.

#### Scenario: Index accessibility
- **WHEN** Helm CLI queries the repository
- **THEN** `https://binhnguyenduc.github.io/helm-charts/index.yaml` MUST be accessible
- **AND** it MUST return a valid Helm repository index format

#### Scenario: Chart metadata
- **WHEN** the index is queried
- **THEN** it MUST include all published chart versions
- **AND** each entry MUST contain name, version, description, and download URL
- **AND** download URLs MUST point to `https://binhnguyenduc.github.io/helm-charts/[chart-name]-[version].tgz`

### Requirement: Chart Package Distribution
The repository SHALL distribute chart packages via direct HTTPS download from GitHub Pages.

#### Scenario: Package download
- **WHEN** Helm CLI downloads a chart
- **THEN** the package MUST be served from `docs/[chart-name]-[version].tgz`
- **AND** it MUST be a valid gzipped tar archive
- **AND** the response MUST include appropriate Content-Type headers

#### Scenario: Version availability
- **WHEN** multiple versions of a chart exist
- **THEN** all versions MUST remain accessible via their versioned URLs
- **AND** the index MUST list all available versions
