---
name: dependency-security
description: "Review project dependencies for known CVEs, outdated packages, and supply chain risks. Analyses package manifests (package.json, pom.xml, build.gradle, requirements.txt, go.mod) for vulnerable versions. Use when the user asks about dependency security, check my dependencies, CVE check, outdated packages, supply chain risks, or vulnerable dependencies."
compatibility: Works with any package manager. No external tools required (analysis is manifest-based).
metadata:
  version: "1.0.0"
---

# Dependency Security — Supply Chain Review

As AI accelerates the pace of development, teams are pulling in more
dependencies faster than ever. A single vulnerable package can undermine
an otherwise secure codebase. Reviewing dependencies early — before they
ship — is one of the highest-leverage security activities you can do.

Review project dependencies for known vulnerabilities, outdated packages,
and supply chain risks by analysing package manifest files.

## Step 1: Discover Package Manifests

Search the project for dependency files:

```bash
find . -maxdepth 4 \( \
  -name "package.json" -o \
  -name "package-lock.json" -o \
  -name "yarn.lock" -o \
  -name "pom.xml" -o \
  -name "build.gradle" -o \
  -name "build.gradle.kts" -o \
  -name "requirements.txt" -o \
  -name "Pipfile" -o \
  -name "Pipfile.lock" -o \
  -name "pyproject.toml" -o \
  -name "poetry.lock" -o \
  -name "go.mod" -o \
  -name "go.sum" -o \
  -name "Gemfile" -o \
  -name "Gemfile.lock" -o \
  -name "Cargo.toml" -o \
  -name "Cargo.lock" -o \
  -name "*.csproj" -o \
  -name "packages.config" \
\) -not -path "*/node_modules/*" -not -path "*/vendor/*" \
   -not -path "*/.git/*" -not -path "*/target/*"
```

If no manifests found, inform the user and stop.

## Step 2: Analyse Dependencies

For each manifest file, review:

### 2a. Known Vulnerable Packages — Live Lookup

**Do NOT rely on training data or static CVE tables.** For every dependency
found in the manifests, fetch live vulnerability data from these sources.

#### OSV.dev API (primary — covers all ecosystems)

For each dependency, query the OSV.dev API:

```
https://api.osv.dev/v1/query
```

POST body:
```json
{
  "version": "<resolved-version>",
  "package": {
    "name": "<package-name>",
    "ecosystem": "<ecosystem>"
  }
}
```

Where `<ecosystem>` is one of: `npm`, `PyPI`, `Maven`, `Go`, `crates.io`,
`RubyGems`, `NuGet`.

For Maven packages, use the format `groupId:artifactId` as the package name
(e.g. `org.apache.logging.log4j:log4j-core`).

Parse the response: if `vulns` array is non-empty, the version is affected.
Extract `id`, `summary`, `severity`, and `affected[].ranges[].events` for
the fix version.

#### Fallback sources (if OSV.dev is unreachable)

Try these in order:
1. **GitHub Advisory Database**: `https://github.com/advisories?query=<package-name>`
2. **NVD**: `https://nvd.nist.gov/vuln/search/results?query=<package-name>`

#### When live data cannot be fetched

If vulnerability data **cannot be fetched** for a dependency (network error,
API unavailable, timeout), emit a warning in the report:

> ⚠️ **Live vulnerability data unavailable for `<package>` (<version>).**
> Could not reach OSV.dev or fallback sources. This dependency was NOT
> verified against known CVEs. Run `npm audit` / `mvn dependency-check:check`
> locally to verify.

**Never silently skip a dependency.** Every dependency must either have a
live lookup result or an explicit warning.

### 2b. Pinning & Lock Files

- Are dependencies pinned to exact versions or using loose ranges?
- Does a lock file exist (`package-lock.json`, `poetry.lock`, etc.)?
- Are there `*` or `latest` version specifiers?

### 2c. Supply Chain Red Flags

- Typosquatting risk: packages with names similar to popular packages
- Packages with very few downloads or maintainers
- Dependencies pulled from non-standard registries
- Post-install scripts in `package.json` that run arbitrary commands
- Git dependencies pointing to non-official repositories

### 2d. Transitive Dependencies

If lock files are available, check transitive (indirect) dependencies for
known vulnerable versions. Focus on high-severity issues only.

## Step 3: Rate Each Finding

| Severity | Criteria |
|----------|----------|
| **CRITICAL** | Known exploited CVE (CISA KEV), RCE, auth bypass |
| **HIGH** | CVE with public exploit, data breach potential |
| **MEDIUM** | CVE requiring specific conditions, information disclosure |
| **LOW** | Outdated but no known exploit, best-practice concerns |

## Step 4: Report

```
# Dependency Security Report

## Data Sources
