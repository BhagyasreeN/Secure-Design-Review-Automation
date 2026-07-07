# File Scanning Strategy — Common SDR Procedures

## Files to SKIP (Never Scan)

**Always skip these patterns to prevent timeouts and false positives:**

```
**/node_modules/**           # NPM dependencies (often 10,000+ files)
**/dist/**, **/build/**      # Compiled/bundled outputs
**/__pycache__/**            # Python bytecode cache
**/*.pyc, **/*.pyo           # Python compiled files
**/venv/**, **/.venv/**      # Python virtual environments
**/vendor/**                 # Go/PHP dependencies
**/.git/**, **/.svn/**       # Version control internals
**/.next/**, **/.nuxt/**     # Next.js/Nuxt.js build cache
**/coverage/**, **/.pytest_cache/**  # Test output
**/*.min.js, **/*.bundle.js  # Minified JavaScript (unreadable)
**/*.map                     # Source maps
**/target/**                 # Maven/Gradle build output
**/bin/**, **/obj/**         # .NET build artifacts
```

## Files to PRIORITIZE (Scan First)

```
PRIORITY 1 (Critical - ALWAYS scan):
- src/**, lib/**, app/**              # Source code
- config/**, *.config.js, *.config.ts # Configuration files
- .env, .env.*, *.properties          # Environment/config files
- Dockerfile, docker-compose.yml      # Container definitions
- *.tf, *.tfvars, *.tf.json          # Terraform IaC
- helm/**, k8s/**, manifests/**      # Kubernetes configs
- package.json, requirements.txt     # Dependency manifests
- pom.xml, build.gradle, *.csproj    # Build files
- .github/workflows/**               # CI/CD pipelines
- artifacts_converted/**             # Converted documents

- ARCHITECTURE.md (case-insensitive)  # Architecture documentation (MANDATORY)
- DESIGN.md (case-insensitive)        # Design documentation (MANDATORY)
- README.md                          # Project overview (MANDATORY)
- docs/architecture/**, docs/design/** # Architecture/design folders
- architecture-docs/**, design-docs/** # Architecture/design folders
- diagrams/**, architecture/**       # Diagram folders
- ADR/**, adr/**, decisions/**       # Architecture Decision Records
- *.drawio, *.drawio.xml             # Draw.io diagrams (if text format)
- system-design.*, architecture.*    # Design documents (any extension)
- docs/security/**, SECURITY.md      # Security documentation
- *.yaml, *.yml (CI/CD configs)      # Pipeline security/config

PRIORITY 2 (Secondary, if time permits):
- docs/** (other documentation)      # General docs
- tests/**, spec/**                  # Test files / security patterns
- scripts/**, tools/**               # Utility scripts
```

## Scanning Order

1. Discover all files in repository (or per component for multi-folder)
2. Filter out SKIP patterns
3. Scan PRIORITY 1 files first
4. If time/token budget permits, scan PRIORITY 2
5. Report counts: `Scanned X files (Y critical, Z secondary, skipped N)`
