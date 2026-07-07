---
name: code-security
description: "Scan source code files for security vulnerabilities including SQL injection, XSS, command injection, insecure deserialisation, hardcoded secrets, and OWASP Top 10 issues. Use when the user asks to check code for security issues, audit this file, find vulnerabilities in my code, OWASP scan, or code security audit."
compatibility: Works with any language. No external tools required.
metadata:
  version: "1.0.0"
---

# Code Security — Static Analysis Review

AI-assisted development is accelerating the rate of code change across
teams. More code, faster — but also more surface area for vulnerabilities.
Performing security reviews early, before issues compound, is critical to
keeping pace without compromising safety.

Perform a manual static-analysis-style review of source code files for
security vulnerabilities.

## Step 1: Identify Scope

Ask the user which files or directories to review, or infer from context.
Reasonable defaults:

- If in a project root, scan `src/`, `app/`, `lib/`, `cmd/`, or equivalent
- Skip `test/`, `__tests__/`, `*_test.*`, `*.spec.*` files
- Skip `node_modules/`, `vendor/`, `target/`, `dist/`, `build/` directories
- Skip documentation and markdown files

List the files to be reviewed and confirm with the user if the scope is large
(> 20 files).

**If no source files are found**, inform the user:

> No source code files found in the current directory. Please specify the
> path to the code you'd like reviewed, or navigate to the project root.

## Step 2: Detect Languages & Frameworks

Identify the languages and frameworks in use. This determines which
vulnerability classes are relevant:

| Language | Key Risk Areas |
|----------|---------------|
| Python | `eval`, `exec`, `pickle`, `subprocess(shell=True)`, SSTI, SQL formatting |
| Java | Deserialisation, JNDI, `Runtime.exec`, SQL concatenation, XXE |
| JavaScript/TS | `eval`, `innerHTML`, `child_process.exec`, prototype pollution |
| Go | `os/exec`, SQL concatenation, `InsecureSkipVerify`, `text/template` |
| C/C++ | Buffer overflow, format string, use-after-free, integer overflow |
| Ruby | `eval`, `system()`, ERB injection, mass assignment |
| PHP | `eval`, `include` with user input, SQL injection, type juggling |

## Step 3: Scan for Vulnerabilities

For each file, check for the following categories:

### 3a. Injection Flaws
- SQL queries built with string concatenation or formatting
- OS command execution with user-controlled arguments
- LDAP, XPath, or NoSQL query injection
- Template injection (SSTI)
- Code injection via `eval`, `exec`, `Function()`, etc.

### 3b. Authentication & Authorisation
- Missing or bypassable authentication checks
- Hardcoded credentials, API keys, or tokens
- Insecure session management
- JWT issues (none algorithm, missing expiry, weak secret)
- IDOR (Insecure Direct Object Reference)

### 3c. Cryptography
- Use of weak algorithms: MD5, SHA1, DES, RC4
- Hardcoded encryption keys or IVs
- Missing TLS/SSL verification
- Predictable random number generation for security purposes
- ECB mode usage

### 3d. Data Exposure
- PII logged in plaintext (names, emails, SSNs, account numbers)
- Secrets/passwords in log output
- Sensitive data in error messages returned to users
- Debug endpoints or verbose error handlers in production paths

### 3e. Deserialisation
- `pickle.loads()`, `yaml.load()` without SafeLoader
- Java `ObjectInputStream.readObject()` on untrusted data
- `JSON.parse` of user input fed into security-sensitive operations
- XML parsing without disabling external entities (XXE)

### 3f. File Operations
- Path traversal (`../`) in file access with user input
- Unrestricted file upload (no type/size validation)
- Symlink following without validation
- Temporary file creation with predictable names

## Step 4: Apply Filters

**Do NOT report:**
- Issues only in test files
- Theoretical issues without a concrete attack path
- Style or best-practice concerns without security impact