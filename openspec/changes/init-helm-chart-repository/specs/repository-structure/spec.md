# Repository Structure Specification

## ADDED Requirements

### Requirement: Package Storage Directory
The repository SHALL maintain a `docs/` directory for storing pre-built Helm chart packages and serving them via GitHub Pages.

#### Scenario: Package organization
- **WHEN** a pre-built chart package is contributed
- **THEN** the package file MUST be placed directly in `docs/` directory
- **AND** it MUST follow naming convention `[chart-name]-[version].tgz`
- **AND** the package MUST be a valid gzipped tar archive

#### Scenario: Multiple package versions
- **WHEN** multiple versions of the same chart exist
- **THEN** each version MUST have a unique filename
- **AND** all versions MUST coexist in `docs/` directory
- **AND** version numbers MUST follow semantic versioning

### Requirement: Repository Index File
The repository SHALL maintain an automatically-generated index file for Helm chart discovery.

#### Scenario: Index file location
- **WHEN** packages are added to the repository
- **THEN** a `docs/index.yaml` file MUST be generated or updated
- **AND** it MUST contain metadata for all chart packages in `docs/`

#### Scenario: Index metadata completeness
- **WHEN** the index is generated
- **THEN** each chart entry MUST include name, version, description, and download URL
- **AND** download URLs MUST point to packages in the same `docs/` directory
- **AND** the index MUST be in valid Helm repository index format

### Requirement: Git Configuration
The repository SHALL maintain proper Git configuration to track published packages.

#### Scenario: Package tracking
- **WHEN** chart packages are added to `docs/`
- **THEN** they MUST be committed to version control
- **AND** commit messages MUST reference chart name and version
- **AND** packages MUST NOT be listed in `.gitignore`

#### Scenario: Clean repository
- **WHEN** the repository is cloned
- **THEN** it MUST NOT contain chart source code or templates
- **AND** it MUST NOT contain build artifacts from chart development
- **AND** it MUST only contain packaged charts ready for distribution

### Requirement: Repository Documentation
The repository SHALL provide clear documentation for package consumers and contributors.

#### Scenario: Consumer documentation
- **WHEN** users access the repository
- **THEN** `README.md` MUST explain how to add the chart repository
- **AND** it MUST provide example command: `helm repo add [name] https://binhnguyenduc.github.io/helm-charts/`
- **AND** it MUST explain how to search and install available charts

#### Scenario: Contributor documentation
- **WHEN** external repositories want to publish packages
- **THEN** `CONTRIBUTING.md` MUST explain the package contribution process
- **AND** it MUST specify how to build and package charts: `helm package [chart-dir]`
- **AND** it MUST describe PR requirements and validation checks
- **AND** it MUST explain version conflict resolution

### Requirement: Landing Page
The repository SHALL provide a human-readable landing page for the chart repository.

#### Scenario: HTML landing page
- **WHEN** users visit `https://binhnguyenduc.github.io/helm-charts/`
- **THEN** they MUST see a `docs/index.html` page
- **AND** it MUST explain the repository purpose
- **AND** it MUST provide usage instructions for adding the repository
- **AND** it SHOULD list or link to available charts
