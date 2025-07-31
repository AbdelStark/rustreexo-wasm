# CI/CD Pipeline Documentation

This document describes the comprehensive CI/CD pipeline set up for the Rustreexo WASM project.

## Overview

The CI/CD pipeline consists of multiple workflows designed to ensure code quality, security, and reliable releases:

- **CI Pipeline**: Continuous integration with testing and validation
- **Build Artifacts**: WASM package building and artifact management  
- **Release Pipeline**: Automated releases with NPM publishing
- **Security Scanning**: Comprehensive security analysis
- **Performance Benchmarks**: Performance tracking and regression detection
- **Dependency Management**: Automated dependency updates via Dependabot

## Workflows

### 1. CI Workflow (`ci.yml`)

**Triggers**: Push to main/develop, Pull requests
**Purpose**: Core testing and validation

**Jobs**:
- **Test Suite**: Multi-platform testing (Ubuntu, Windows, macOS) with Rust stable/beta
- **WASM Build Test**: Validates all WASM targets build successfully
- **Examples**: Tests Node.js and browser examples
- **Documentation**: Builds and uploads Rust documentation

**Key Features**:
- Matrix testing across platforms and Rust versions
- WASM-specific testing with headless browser
- Comprehensive test coverage including examples
- Efficient caching for faster builds

### 2. Build Artifacts Workflow (`build-artifacts.yml`)

**Triggers**: Push to main, Tags, Manual dispatch
**Purpose**: Build and publish WASM packages as artifacts

**Jobs**:
- **Build WASM**: Creates packages for all targets (web, nodejs, bundler, no-modules)
- **Test Artifacts**: Validates built packages across platforms
- **Package Info**: Generates detailed package information and checksums

**Artifacts Generated**:
- Individual target packages with version tagging
- Combined package containing all targets
- Package information with sizes and checksums
- 90-day retention for releases, 30-day for others

### 3. Release Workflow (`release.yml`)

**Triggers**: Version tags (v*.*.*), Manual dispatch
**Purpose**: Automated releases with NPM publishing

**Jobs**:
- **Prepare Release**: Version extraction and prerelease detection
- **Build Release**: Creates release packages and archives
- **Publish NPM**: Publishes to NPM registry with proper tagging
- **GitHub Release**: Creates GitHub release with detailed notes
- **Post-Release**: Creates tracking issue for post-release tasks

**Features**:
- Semantic version handling with prerelease support
- Multi-target NPM publishing with scoped packages
- Automated release notes generation
- Comprehensive file checksums and package verification
- Post-release tracking and monitoring

### 4. Security Workflow (`security.yml`)

**Triggers**: Push to main, Pull requests, Daily schedule, Manual dispatch
**Purpose**: Comprehensive security analysis

**Jobs**:
- **Security Audit**: Rust crate vulnerability scanning with cargo-audit
- **Dependency Review**: PR-based dependency security analysis  
- **CodeQL Analysis**: Static code analysis for Rust and JavaScript
- **Supply Chain**: License compliance and dependency verification
- **Secrets Scanning**: Repository secrets detection with TruffleHog
- **Container Security**: Docker image vulnerability scanning with Trivy
- **Security Scorecard**: OSSF security scorecard analysis

**Features**:
- Daily automated security scans
- PR-based security review for new dependencies
- Multiple security tools integration
- SARIF format results for GitHub Security tab
- Software Bill of Materials (SBOM) generation

### 5. Performance Benchmarks Workflow (`benchmark.yml`)

**Triggers**: Push to main, Pull requests, Weekly schedule, Manual dispatch
**Purpose**: Performance tracking and regression detection

**Jobs**:
- **Rust Benchmarks**: Native Rust performance with Criterion
- **WASM Benchmarks**: Node.js WASM performance testing
- **Browser Benchmarks**: Headless browser WASM performance
- **Performance Comparison**: Cross-platform performance analysis

**Features**:
- Multiple execution environments (native, Node.js, browser)
- Automated performance regression detection
- PR comments with benchmark results
- Historical performance tracking
- Statistical analysis with multiple iterations

## Dependency Management

### Dependabot Configuration (`dependabot.yml`)

**Automated Updates**:
- **Rust Dependencies**: Weekly updates with intelligent grouping
- **NPM Dependencies**: Separate handling for dev vs production dependencies
- **GitHub Actions**: Weekly action updates

**Features**:
- Grouped updates to reduce PR noise
- Security-focused dependency prioritization
- Automatic reviewer assignment
- Consistent commit message formatting

## Required Secrets

For full functionality, configure these repository secrets:

### NPM Publishing
- `NPM_TOKEN`: NPM authentication token for package publishing
  - Scope: Must have publish access to @rustreexo organization
  - Type: Automation token recommended

### Additional Secrets (Optional)
- Repository uses default `GITHUB_TOKEN` for most operations
- Additional secrets may be needed for external integrations

## Environment Setup

### Required Permissions

The workflows require these repository permissions:
- `contents: write` - For creating releases and updating repository
- `security-events: write` - For uploading security scan results
- `packages: write` - For publishing packages
- `actions: read` - For accessing workflow information

### Protected Branches

Recommended branch protection rules for `main`:
- Require PR reviews before merging
- Require status checks to pass before merging
- Require branches to be up to date before merging
- Required status checks:
  - `Test Suite (ubuntu-latest, stable)`
  - `WASM Build Test`
  - `Test Examples`

## Usage Examples

### Triggering a Release

1. **Tag-based Release** (Recommended):
   ```bash
   git tag v1.0.0
   git push origin v1.0.0
   ```

2. **Manual Release**:
   - Go to Actions → Release workflow
   - Click "Run workflow"
   - Enter version (e.g., v1.0.0)

### Running Specific Benchmarks

```bash
# Via GitHub Actions UI
Actions → Performance Benchmarks → Run workflow
# Select benchmark suite: all, rust, wasm, or comparison
```

### Security Scan On-Demand

```bash
# Via GitHub Actions UI  
Actions → Security → Run workflow
```

## Monitoring and Alerts

### Workflow Notifications

- Failed workflows automatically create GitHub notifications
- Security findings are reported in GitHub Security tab
- Release workflows create tracking issues

### Performance Monitoring

- Benchmark results are automatically commented on PRs
- Performance regressions are highlighted in reports
- Historical data is preserved in workflow artifacts

## Troubleshooting

### Common Issues

1. **NPM Publishing Failures**:
   - Verify `NPM_TOKEN` secret is valid
   - Check package name availability
   - Ensure version hasn't been published already

2. **WASM Build Failures**:
   - Check Rust toolchain compatibility
   - Verify wasm-pack version compatibility
   - Review target-specific build requirements

3. **Security Scan Failures**:
   - Review security findings in GitHub Security tab
   - Update vulnerable dependencies
   - Review and dismiss false positives if needed

### Debugging Workflows

1. Enable debug logging by setting repository variable:
   - `ACTIONS_STEP_DEBUG` = `true`

2. Review workflow run logs in Actions tab

3. Download artifacts for detailed analysis

## Maintenance

### Regular Tasks

- **Weekly**: Review Dependabot PRs and security alerts
- **Monthly**: Review and update workflow configurations
- **Quarterly**: Audit security scan results and update configurations
- **Per Release**: Monitor release workflow success and NPM package availability

### Configuration Updates

When updating workflows:
1. Test changes in a feature branch first
2. Review workflow syntax with GitHub Actions validation
3. Monitor first runs carefully for any issues
4. Update this documentation as needed

## Security Considerations

- All workflows use pinned action versions for security
- Minimal required permissions are specified for each job
- Secrets are only accessible to jobs that need them
- All external dependencies are verified and cached
- Security scans run on all code changes and dependencies

---

For questions or issues with the CI/CD pipeline, please create an issue in the repository.