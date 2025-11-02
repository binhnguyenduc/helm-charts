# CI/CD Automation Specification

## ADDED Requirements

### Requirement: Package Validation on Pull Requests
The repository SHALL validate pre-built chart packages on pull requests before allowing merge.

#### Scenario: Package integrity check
- **WHEN** a pull request adds or modifies .tgz files in `docs/` directory
- **THEN** GitHub Actions MUST verify the file is a valid gzipped tar archive
- **AND** the workflow MUST extract and parse Chart.yaml from the package
- **AND** the PR MUST NOT be mergeable if integrity check fails

#### Scenario: Metadata validation
- **WHEN** a package is validated on PR
- **THEN** GitHub Actions MUST verify Chart.yaml contains required fields (name, version, apiVersion)
- **AND** it MUST verify version follows semantic versioning format
- **AND** it MUST verify apiVersion is compatible with Helm 3 (v2)

#### Scenario: Version conflict detection
- **WHEN** a pull request adds a package
- **THEN** GitHub Actions MUST check if the same chart name and version already exists in `docs/`
- **AND** the PR MUST fail validation if a version conflict is detected
- **AND** the workflow MUST report which existing file conflicts

#### Scenario: Fast feedback
- **WHEN** validation runs on PR
- **THEN** results MUST be reported within 3 minutes
- **AND** status MUST be visible in the PR checks section
- **AND** failure messages MUST clearly explain the validation issue

### Requirement: Repository Index Management
The repository SHALL automatically update the chart repository index when new packages are merged.

#### Scenario: Index regeneration on merge
- **WHEN** a pull request adding packages is merged to main
- **THEN** GitHub Actions MUST regenerate `docs/index.yaml`
- **AND** it MUST use command `helm repo index docs/ --url https://binhnguyenduc.github.io/helm-charts/`
- **AND** it MUST run after the PR merge completes

#### Scenario: Incremental index updates
- **WHEN** the index is regenerated
- **THEN** existing chart entries MUST be preserved
- **AND** new package entries MUST be added
- **AND** the index MUST remain in valid YAML format
- **AND** all package URLs MUST be correctly formatted

#### Scenario: Index commit automation
- **WHEN** index regeneration completes
- **THEN** GitHub Actions MUST commit `docs/index.yaml` if it changed
- **AND** the commit message MUST indicate index update
- **AND** the commit MUST be pushed to main branch automatically

### Requirement: Automated GitHub Pages Deployment
The repository SHALL automatically deploy updated packages and index to GitHub Pages.

#### Scenario: Automatic deployment trigger
- **WHEN** changes are pushed to `docs/` directory on main branch
- **THEN** GitHub Pages MUST automatically rebuild and deploy
- **AND** deployment MUST complete within 5 minutes
- **AND** updated index and packages MUST be accessible via HTTPS

#### Scenario: Deployment verification
- **WHEN** GitHub Pages deployment completes
- **THEN** `https://binhnguyenduc.github.io/helm-charts/index.yaml` MUST return updated index
- **AND** newly added packages MUST be downloadable from their URLs
- **AND** Helm CLI MUST be able to refresh repository and discover new charts

### Requirement: Workflow Isolation
The repository SHALL separate package validation from index management to ensure safety.

#### Scenario: PR validation workflow scope
- **WHEN** PR validation workflow runs
- **THEN** it MUST only validate package integrity and metadata
- **AND** it MUST NOT modify `docs/index.yaml`
- **AND** it MUST NOT commit any changes
- **AND** it MUST only report validation status

#### Scenario: Index update workflow scope
- **WHEN** index update workflow runs
- **THEN** it MUST only trigger on pushes to main branch affecting `docs/*.tgz`
- **AND** it MUST NOT run on pull requests
- **AND** it MUST only regenerate index and commit if changed

#### Scenario: Workflow triggering
- **WHEN** a PR is opened with package changes
- **THEN** only validation workflow MUST run
- **WHEN** the PR is merged to main
- **THEN** only index update workflow MUST run
- **AND** workflows MUST NOT trigger each other in a loop

### Requirement: Workflow Permissions
The repository SHALL configure GitHub Actions with minimal required permissions following principle of least privilege.

#### Scenario: Validation workflow permissions
- **WHEN** package validation workflow runs
- **THEN** it MUST have read-only access to repository contents
- **AND** it MUST NOT have write permissions
- **AND** it MUST NOT have access to secrets (unless required for specific checks)

#### Scenario: Index update workflow permissions
- **WHEN** index update workflow runs
- **THEN** it MUST have write permission to commit to main branch
- **AND** it MUST use GITHUB_TOKEN for authentication
- **AND** it MUST configure git user.name and user.email for automated commits
- **AND** commits MUST be attributed to GitHub Actions bot

#### Scenario: Protected branch compatibility
- **WHEN** main branch has protection rules
- **THEN** index update workflow MUST use a token with permission to bypass protections OR
- **THEN** workflow MUST create a separate commit that follows protection rules
- **AND** workflow configuration MUST document required permissions
