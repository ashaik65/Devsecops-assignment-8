# Dynamic Application Security Testing (DAST) with OWASP ZAP

---

## 1. Introduction

Dynamic Application Security Testing (DAST) is a black-box testing technique that analyzes a running application to identify security vulnerabilities by interacting with its exposed interfaces. Unlike Static Application Security Testing (SAST), which inspects source code, DAST simulates real-world attacks on the live application to find runtime vulnerabilities such as SQL Injection, Cross-Site Scripting (XSS), and authentication flaws.

---

## 2. Objective

The goal of this assignment is to integrate OWASP ZAP, a widely-used open-source DAST tool, into a CI/CD pipeline. This integration allows automated scanning of a web application to identify security issues and generate actionable reports.

---

## 3. How DAST Works

- **Black-box approach:** Tests the application without access to source code.
- **Simulated attacks:** Injects malicious inputs, manipulates sessions, and crawls the application.
- **Dynamic analysis:** Detects vulnerabilities that manifest only during runtime.
- **Reporting:** Provides detailed findings with severity ratings and remediation advice.

---

## 4. Tool Selection: OWASP ZAP

OWASP ZAP (Zed Attack Proxy) is an open-source DAST tool offering:

- Automated full and partial scanning capabilities.
- Support for scanning both web and API endpoints.
- Seamless integration with CI/CD pipelines (e.g., GitHub Actions).
- Extensibility via scripts and add-ons.

---

## 5. Integration Steps

### 5.1 Local Installation and Manual Testing

- Installed OWASP ZAP on the local machine.
- Conducted manual exploratory scans against the Juice Shop application using the ZAP GUI.

## Manual Scan Screenshot via ZAP UI
![alt text](<zap manual testing.png>)

## Alerts found while doing manual Scan (SQL injection, XSS, CDM)
![alt text](<Zap alerts new.png>)

## Automated Scan screenhsot via ZAP UI
![alt text](<automated scan zap.png>)

## Alerts found while doing auomated Scan (SQL injection, XSS, CDM)

![alt text](<auotmated alerts scan.png>)

### 5.2 Automated Scanning via GitHub Actions Pipeline

- Created a GitHub Actions workflow to run OWASP ZAP full scans using the official containerized action.
- Configured the scan target URL as `https://juice-shop.herokuapp.com`.
- Passed authentication headers when required via environment variables.
- Disabled SSL certificate validation for development environments to prevent scan interruptions.

## Github action Pipeline Run
![alt text](<pipeline steps.png>)

## Pipeline artifacts
![alt text](<artifcats from pipline.png>)

**Pipeline configuration snippet:**

```yaml
name: ZAP DAST Scan

on:
  push:
    branches: [master]
  pull_request:
    branches: [master]
  workflow_dispatch:

jobs:
  zap_scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Run ZAP Full Scan
        uses: zaproxy/action-full-scan@v0.12.0
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          target: 'https://juice-shop.herokuapp.com'
          cmd_options: '-a -z "-config connection.ssl_cert_validation=false"'
          allow_issue_writing: false

      - name: Upload Scan Artifacts
        uses: actions/upload-artifact@v4
        with:
          name: zap-dast-reports
          path: |
            report_html.html
            report_json.json
            zap.log
```

# 🛡️ 6. Report on Findings and Mitigation

## 6.1 Key Vulnerabilities Identified

| Vulnerability             | Severity | Description                                  | Mitigation                                                                 |
|---------------------------|----------|----------------------------------------------|----------------------------------------------------------------------------|
| Cross-Site Scripting (XSS)| High     | Unsanitized input allows script injection    | Sanitize and validate all user inputs; apply Content Security Policy (CSP).|
| SQL Injection             | High     | Injection of malicious SQL commands          | Use parameterized queries or prepared statements.                          |
| Security Misconfiguration | Medium   | Missing or incorrect security headers        | Harden server configuration and add security headers (e.g., HSTS, CSP).   |
| Sensitive Data Exposure   | Medium   | Information leakage through error messages   | Avoid verbose error messages; implement proper logging and monitoring.     |

## 6.2 Reports of Automated Scan from ZAP UI

![alt text](<zap report.png>)

## 6.3 CI/CD Scan Logs and Reports

![alt text](<github action  report.png>)

# 🧪 7. Pros and Cons of DAST

| Pros                                                | Cons                                                        |
|-----------------------------------------------------|-------------------------------------------------------------|
| Tests the running application in real-time          | Cannot detect certain code-level issues                    |
| Identifies vulnerabilities visible only at runtime  | May produce false positives requiring manual review         |
| Easy to integrate with CI/CD pipelines              | Limited insight into internal source code                  |
| Does not require access to source code              | Scans can be time-consuming on large apps                  |

---

# 🚫 8. Limitations of DAST

- Unable to detect vulnerabilities that require source code analysis (e.g., certain logic flaws).
- May miss issues hidden behind complex workflows or requiring authentication unless properly configured.
- Limited to the exposed surface area of the application; unexposed code paths remain untested.
- False positives may require manual verification and triage.
- Scanning large applications can be resource-intensive and slow.

---

# 📚 9. Lessons Learned

- DAST is crucial for identifying runtime security risks that static analysis tools may miss.
- Integrating DAST in CI/CD pipelines ensures continuous security validation with each build or deployment.
- Proper configuration of scan options (authentication, SSL settings) is essential to reduce false positives and maximize coverage.
- Manual exploratory testing complements automated scans to uncover more complex issues.
- Continuous monitoring and periodic rescanning maintain security posture over time.

---

# ⚠️ 10. Why DAST is Needed Today

- Modern web applications are increasingly complex and exposed to diverse attack surfaces.
- New vulnerabilities are discovered daily; automated runtime scanning helps detect issues promptly.
- DevSecOps promotes embedding security testing early and often in the development lifecycle.
- DAST complements SAST and manual testing for comprehensive application security.
- Automated DAST scans assist organizations in meeting regulatory compliance and security standards.

---

# 🔗 11. References

- [OWASP ZAP Official Documentation](https://www.zaproxy.org/docs/)
- [OWASP Juice Shop](https://owasp.org/www-project-juice-shop/)
- [GitHub Actions - OWASP ZAP Integration](https://github.com/marketplace/actions/owasp-zap-full-scan)
