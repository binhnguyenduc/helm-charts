# Design: Helm Chart Repository Initialization

## Context
This repository serves as a **centralized aggregation point** for pre-built Helm chart packages from multiple external repositories. Chart development and packaging happen in their respective source repositories. This repository receives packaged charts (.tgz files), validates them, maintains a unified index, and distributes them via GitHub Pages.

**Stakeholders**: External chart maintainers, Helm users, CI/CD systems from source repositories

**Constraints**:
- GitHub Pages 1GB repository limit
- Helm 3.x compatibility required
- HTTPS required for secure chart distribution
- Must support packages from multiple source repositories
- No chart source code or templates in this repository

## Goals / Non-Goals

**Goals**:
- Accept pre-built chart packages from external repositories
- Automated package validation (integrity, format, metadata)
- Automated repository index management
- Reliable chart distribution via GitHub Pages
- Support for multiple charts from different sources
- Simple contribution workflow for external repositories

**Non-Goals**:
- Chart development or templating (done in source repositories)
- Chart linting or template validation (done before packaging)
- Chart signing/provenance (can be added later)
- Custom domain configuration (use default GitHub Pages URL)
- Chart mirroring or CDN integration
- Private/authenticated chart access

## Decisions

### Decision 1: Package Aggregation Model
**What**: Repository receives pre-built chart packages (.tgz) from external repositories, not chart source code

**Why**:
- Separation of concerns: Chart development vs. distribution
- Source repositories maintain full control over their charts
- This repository focuses on aggregation and distribution only
- Reduces repository size (no template files, only packages)
- Enables multiple teams to contribute independently

**Alternatives considered**:
- Monorepo with all chart sources: Scales poorly, tight coupling
- Submodules for each chart: Complex dependency management

### Decision 2: GitHub Pages from `docs/` Directory
**What**: Use `docs/` directory on `main` branch for GitHub Pages instead of `gh-pages` branch

**Why**:
- Simpler workflow - no branch switching required
- Chart packages and index in same branch
- Easier local testing and debugging
- Native Git history for published packages

**Alternatives considered**:
- `gh-pages` branch: More complex, requires branch management
- Root directory: Clutters repository root with packages

### Decision 3: PR-Based Package Contribution
**What**: External repositories contribute packages via pull requests with .tgz files

**Why**:
- Code review process for package quality
- Visibility into what's being published
- Version conflict detection before merge
- Manual override capability for special cases
- Simple integration with existing Git workflows

**Alternatives considered**:
- Automated push from external CI/CD: Security concerns, harder to audit
- Manual upload interface: Doesn't scale, no version control
- Git submodules: Complex, doesn't fit package aggregation model

### Decision 4: Package Validation Instead of Chart Linting
**What**: Validate packaged .tgz files (integrity, format, metadata) instead of linting chart templates

**Why**:
- Chart templates already validated in source repository
- Focus on package integrity and index compatibility
- Faster validation (no template rendering)
- Prevents corrupted or malformed packages

**Alternatives considered**:
- Re-lint chart templates: Redundant, requires unpacking
- No validation: Risk of broken packages
- Full integration testing: Too slow for PR feedback

### Decision 5: Automated Index Management
**What**: Use `helm repo index` to regenerate `docs/index.yaml` automatically on package merge

**Why**:
- Official Helm tooling ensures format correctness
- Automatic metadata extraction from package
- Supports multiple chart versions from different sources
- Handles dependencies correctly
- Incremental updates preserve existing entries

**Alternatives considered**:
- Manual index.yaml: Error-prone, doesn't scale
- Custom indexing script: Unnecessary complexity
- Index-per-source: Fragments discovery, poor UX

## Architecture

### Directory Structure
```
helm-charts/
├── docs/                      # GitHub Pages hosting
│   ├── index.html            # Repository landing page
│   ├── index.yaml            # Helm chart index (auto-generated)
│   └── [chart-name]-[version].tgz  # Pre-built chart packages
├── .github/
│   └── workflows/
│       ├── validate-package.yml    # PR validation
│       └── update-index.yml        # Index regeneration
├── CONTRIBUTING.md           # Package contribution guidelines
└── README.md                 # Repository usage
```

### CI/CD Flow

**PR Workflow** (validate-package.yml):
1. Trigger: Pull request adding/modifying .tgz files in docs/
2. Checkout code
3. Install Helm
4. Validate package integrity: check .tgz is valid gzip archive
5. Extract and validate Chart.yaml metadata
6. Check for version conflicts with existing packages
7. Report validation results to PR

**Index Update Workflow** (update-index.yml):
1. Trigger: Push to main branch with changes to docs/*.tgz
2. Checkout code
3. Install Helm
4. Regenerate index: `helm repo index docs/ --url https://binhnguyenduc.github.io/helm-charts/`
5. Commit index.yaml changes (if any)
6. Push to main branch
7. GitHub Pages automatically deploys

### Package Contribution Flow
1. External repository builds and packages chart: `helm package [chart-dir]`
2. Contributor creates PR adding `[chart-name]-[version].tgz` to `docs/`
3. PR validation workflow checks package integrity and metadata
4. PR reviewed and merged to main
5. Index update workflow regenerates `docs/index.yaml`
6. GitHub Pages serves updated content
7. Users run `helm repo add myrepo https://binhnguyenduc.github.io/helm-charts/`
8. Users install charts with `helm install myapp myrepo/[chart-name]`

### Alternative: Automated Package Publishing
For trusted source repositories, packages can be automatically published:
1. Source repo CI/CD builds and packages chart
2. Source repo CI/CD creates PR to this repository via GitHub API
3. Validation workflow runs automatically
4. Auto-merge on validation success (requires repository configuration)
5. Index updates automatically

## Risks / Trade-offs

### Risk: GitHub Pages Build Delays
**Impact**: Charts may not be immediately available after merge
**Mitigation**: GitHub Pages typically builds within 1-2 minutes; document expected delay

### Risk: Repository Size Growth
**Impact**: Multiple chart versions accumulate in docs/ directory
**Mitigation**:
- Monitor repository size
- Implement cleanup strategy for old versions (future enhancement)
- GitHub Pages 1GB limit is generous for most use cases

### Risk: Concurrent Merges
**Impact**: Two simultaneous merges might conflict in docs/
**Mitigation**:
- CI/CD pulls latest before committing
- Use atomic commits
- GitHub protected branches can enforce sequential merges

### Trade-off: Simplicity vs. Features
**Decision**: Start with minimal feature set
**Rationale**:
- Simple setup reduces maintenance burden
- Features like signing, provenance can be added incrementally
- Focus on core functionality first

## Migration Plan

**Initial Setup** (this proposal):
1. Create directory structure
2. Deploy GitHub Pages configuration
3. Implement CI/CD workflows
4. Validate with test chart

**No migration needed** - this is initial setup

**Rollback**: If GitHub Pages setup fails, simply disable in repository settings

## Open Questions

None - all requirements are clear for initial setup. Future enhancements (chart signing, cleanup automation) can be addressed in separate proposals.
