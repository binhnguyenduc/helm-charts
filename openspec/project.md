# Project Context

## Purpose
A Helm chart repository hosted on GitHub Pages to distribute and manage Kubernetes application packages. This repository serves as a centralized location for publishing, versioning, and sharing Helm charts that can be easily consumed by Kubernetes users and operators.

## Tech Stack
- **Helm**: Package manager for Kubernetes (Chart format v3)
- **GitHub Pages**: Static site hosting for chart repository index
- **GitHub Actions**: CI/CD for chart linting, testing, and publishing
- **YAML**: Chart configuration and values files
- **Kubernetes**: Target deployment platform

## Project Conventions

### Code Style
- **Chart Naming**: Use kebab-case for chart names (e.g., `my-application`, `web-server`)
- **File Structure**: Follow standard Helm chart directory structure:
  ```
  charts/
  └── [chart-name]/
      ├── Chart.yaml        # Chart metadata
      ├── values.yaml       # Default configuration values
      ├── templates/        # Kubernetes manifests
      ├── charts/          # Chart dependencies (if any)
      └── README.md        # Chart documentation
  ```
- **YAML Formatting**: 2-space indentation, no tabs
- **Comments**: Document all configurable values in `values.yaml`
- **Version Format**: Semantic versioning (MAJOR.MINOR.PATCH)

### Architecture Patterns
- **Repository Structure**:
  - `charts/` - Source charts organized by name
  - `docs/` - GitHub Pages site (auto-generated chart index)
  - `.github/workflows/` - CI/CD automation
- **Chart Dependencies**: Use `dependencies` field in Chart.yaml, avoid manual subcharts
- **Configuration**: Provide sensible defaults in values.yaml, allow overrides
- **Templates**: Use named templates for reusable components
- **Release Pattern**: Automated chart packaging and index update on version tags

### Testing Strategy
- **Lint Checks**: `helm lint` for chart validation
- **Template Testing**: `helm template` to verify manifest generation
- **Dry Run**: `helm install --dry-run` for deployment validation
- **Schema Validation**: JSON schema for values.yaml validation
- **CI Pipeline**: Automated testing on pull requests before merge

### Git Workflow
- **Branch Strategy**:
  - `main` - Production-ready charts
  - `spec/*` - OpenSpec proposals
  - `feature/*` - Chart development and updates
- **Commit Conventions**:
  - `feat(chart-name): description` - New charts or features
  - `fix(chart-name): description` - Bug fixes
  - `docs(chart-name): description` - Documentation updates
  - `chore: description` - Repository maintenance
- **Release Process**:
  1. Update Chart.yaml version
  2. Commit changes to feature branch
  3. Create PR with chart changes
  4. Merge to main after approval
  5. CI automatically packages and publishes to GitHub Pages

## Domain Context

### Helm Chart Repository Fundamentals
- **Chart Repository**: HTTP server hosting `index.yaml` and packaged charts (.tgz files)
- **Index File**: `index.yaml` contains metadata for all charts and versions
- **Chart Package**: Compressed tarball containing chart files
- **Repository URL**: `https://binhnguyenduc.github.io/helm-charts/`

### GitHub Pages Integration
- GitHub Pages serves static files from `gh-pages` branch or `docs/` directory
- Chart index and packages published automatically via CI/CD
- HTTPS enabled by default for secure chart distribution

### Chart Lifecycle
1. **Development**: Create/modify chart in `charts/[name]/`
2. **Testing**: Validate locally with `helm lint` and `helm install --dry-run`
3. **Versioning**: Update `Chart.yaml` version following semver
4. **Publishing**: CI packages chart and updates repository index
5. **Consumption**: Users add repository with `helm repo add`

## Important Constraints

### Technical Constraints
- **Kubernetes Compatibility**: Charts must support specified K8s versions (define in Chart.yaml)
- **Helm Version**: Target Helm 3.x (no Tiller support)
- **GitHub Pages Limits**: 1GB repository size, 100GB monthly bandwidth
- **Chart Size**: Keep individual charts under 50MB for reasonable download times
- **Backward Compatibility**: Maintain compatibility within major versions

### Quality Requirements
- All charts must pass `helm lint` without errors
- Required fields in Chart.yaml: name, version, apiVersion, description
- All configurable values must be documented
- README.md required for each chart with usage examples
- CHANGELOG.md recommended for tracking chart changes

### Security Constraints
- No hardcoded secrets or credentials in chart files
- Use Kubernetes secrets for sensitive data
- Document required RBAC permissions
- Scan chart dependencies for vulnerabilities

## External Dependencies

### Required Services
- **GitHub**: Repository hosting and GitHub Pages
- **GitHub Actions**: CI/CD automation for chart publishing

### Chart Dependencies
- Defined per-chart in `Chart.yaml` dependencies section
- External charts pulled from public Helm repositories
- Lock file (`Chart.lock`) tracks dependency versions

### Consumer Requirements
- **Helm CLI**: Version 3.x or higher
- **Kubernetes Cluster**: Version compatibility specified per chart
- **kubectl**: For cluster interaction and verification
