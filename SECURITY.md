# Security

## Reporting Vulnerabilities

If you believe you have found a security issue in the CKEditor software, please contact us immediately.

When reporting a suspected security problem, please bear this in mind:

- Make sure to provide as many details as possible about the vulnerability.
- Please do not disclose publicly any security issues until we fix them and publish security releases.

Contact the security team at security@cksource.com. As soon as we receive the security report, we'll work promptly to confirm the issue and then to provide a security fix.

---

## AI Agent Security Standard

This section defines mandatory security principles for all AI coding assistants operating in this repository.

**Authority:** Derived from [Zendesk Minimum Baseline Security Standard](https://docs.google.com/document/d/17GZ9TpjKCt6WCdw3yxL44Ra_YscbOBVnVkUVGgx5Hz0/) and internal security policies.

### Core Security Mandate

Security is a first-class requirement. Every code suggestion must be evaluated against these guidelines. If a request would result in insecure code:

1. **Stop** and flag the security concern
2. **Explain** why it's problematic
3. **Propose** a secure alternative

AI-generated code requires human review before merging.

### Absolute Prohibitions

AI agents must **NEVER** do the following:

#### Secrets & Credentials
- Hardcode secrets, credentials, API keys, tokens, or passwords in source code
- Store secrets in version control (`.env` files, config with real values)
- Log, print, or expose secret values in any output
- Expose credentials in URLs, error messages, or console output

#### Security Controls
- Disable or weaken security controls
- Bypass authentication or authorization checks
- Disable certificate validation
- Introduce `eval()` or `exec()` with user-supplied content

#### Dangerous Patterns
- Use deprecated cryptographic algorithms (MD5, SHA-1, DES, RC4, ECB mode)
- Generate HTML via raw string interpolation from untrusted input (XSS risk)
- Introduce arbitrary script execution paths

#### Data Exposure
- Log sensitive data (PII, credentials, tokens, customer data)
- Expose stack traces or internal paths to end users
- Transmit sensitive data over unencrypted channels

### Required Security Patterns

#### Content from External Sources
This library renders rich text content inside CKEditor. Content passed via the `data` prop or `config.initialData` must be treated as potentially untrusted. Use CKEditor's built-in sanitization — do not bypass it with raw `innerHTML` assignment outside of the editor API.

```js
// Correct: pass content through CKEditor's API
editor.setData( sanitizedContent );

// NEVER: bypass CKEditor and write to DOM directly
domElement.innerHTML = userContent;
```

#### Error Handling — No Internal Details to Users
```js
// Correct: surface a safe error code, log details internally
this.props.onError( new Error( 'Editor failed to initialize' ), { phase: 'initialization', willEditorRestart: false } );

// NEVER: expose internal paths or stack traces in user-facing messages
console.error( 'Failed at ' + __filename + ':' + error.stack );
```

#### No Hardcoded Credentials
```js
// Correct: receive config from the consuming application
const licenseKey = this.props.config.licenseKey;

// NEVER: hardcode any key or token
const licenseKey = 'ABC123-hardcoded-key';
```

### Security Requirements by Domain

#### Input / Output Handling
- All rich text content must flow through CKEditor's documented API (`setData`, `getData`) — never manipulate `innerHTML` outside the editor
- Validate and sanitize external configuration objects before passing to `editor.create()`
- Do not expose editor internals (model, view, schema) directly in component props

#### Dependency Security
- All CKEditor 5 packages are peer/dev dependencies — keep them up to date
- Enable Dependabot (or equivalent) for automated vulnerability scanning
- Do not pin to versions with known CVEs; use version ranges from `package.json`

#### Build Artifacts
- The `dist/` directory is a build artifact — never commit secrets or sensitive data there
- The published npm package contains only `dist/` — verify contents before releasing

#### Cryptography
| Use Case | Approved | Forbidden |
|----------|----------|-----------|
| Integrity hashing | SHA-256, SHA-3 | MD5, SHA-1 |
| TLS | TLS 1.2+ | SSLv2/3, TLS 1.0/1.1 |

### When to Stop and Escalate

Stop, explain the concern, and recommend involving Security if a task requires:

- Bypassing CKEditor's content sanitization
- Rendering arbitrary HTML/script content outside the editor API
- Disabling certificate validation or TLS
- Exposing customer data or internal system details
- Using deprecated cryptographic algorithms
- Publishing to npm without proper version review

### Security Testing

When generating features, include:

- Tests that verify `onError` is called (not thrown) for initialization and runtime failures
- Tests that verify the `disabled` prop correctly enables/disables read-only mode via the lock mechanism
- Tests for edge cases: `null` editor, rapid mount/unmount, missing `CKEDITOR_VERSION`

### References

- [Minimum Baseline Security Standard](https://docs.google.com/document/d/17GZ9TpjKCt6WCdw3yxL44Ra_YscbOBVnVkUVGgx5Hz0/)
- [Cryptography Standards](https://techmenu.zende.sk/standards/cryptography-standards/)
- [CKEditor 5 Security documentation](https://ckeditor.com/docs/ckeditor5/latest/framework/guides/security/security.html)

---

**Questions?** Reach out to the Security team or file a ticket via the Security Engagement process.
