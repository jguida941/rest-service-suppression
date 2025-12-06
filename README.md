# Vulnerability Suppression Documentation

## How to Run and Locate Reports

- Run from `rest-service`: `mvn verify`
- Reports: `rest-service/target/dependency-check-report.html` (and `-aggregate` if generated)
- Suppression file: `rest-service/suppression.xml` (referenced by `pom.xml`)
- For the assignment deliverables, keep both HTML reports:
  - **Before** suppression: shows all findings.
  - **After** suppression: shows suppressed vulnerabilities (see “Vulnerabilities Suppressed” section in the HTML) removed from the main findings list.

## Context (why certain CVEs are non-exploitable here)

- Java 8 (JDK 1.8)
- Spring Boot executable JAR (not WAR on Tomcat)
- Spring MVC only (no WebFlux, no STOMP/WebSocket)
- Not deployed to Cloud Foundry
- Logging: no SMTP appender, no logback remote receiver

**Note:** These are not global false positives—the versions are in vulnerable ranges. We suppress because the exploit paths are not reachable in this app’s runtime/deployment.

## Suppressed CVEs (non-exploitable in this app)

| CVE                           | Component                            | Why not exploitable here                                        |
|-------------------------------|--------------------------------------|-----------------------------------------------------------------|
| CVE-2022-22965 (Spring4Shell) | spring-core/web/webmvc 5.2.3.RELEASE | Requires JDK 9+ and WAR on Tomcat; app is JDK 8, fat JAR        |
| CVE-2021-22118                | Spring Framework 5.2.3.RELEASE       | WebFlux-only; app uses MVC                                      |
| CVE-2022-22971                | Spring Framework 5.2.3.RELEASE       | Requires STOMP/WebSocket; none configured                       |
| CVE-2023-20873                | Spring Boot 2.2.4.RELEASE            | Cloud Foundry–specific; not deployed there                      |
| CVE-2020-9488                 | log4j-api 2.12.1                     | SMTP appender only; not configured                              |
| CVE-2021-42550                | logback-core 1.2.3                   | Needs write access to logback config; configs bundled/read-only |
| CVE-2023-6378                 | logback-core 1.2.3                   | Remote receiver feature not used                                |
| CVE-2023-1932                 | hibernate-validator 6.0.18.Final     | @SafeHtml not used                                              |
| CVE-2020-10693                | hibernate-validator 6.0.18.Final     | No user input interpolated in messages                          |
| CVE-2025-35036                | hibernate-validator 6.0.18.Final     | Same pattern as above; not used                                 |
| CVE-2023-35116                | jackson-databind 2.10.2              | Vendor-disputed; no untrusted cyclic structures here            |
| CVE-2016-1000027              | Spring Framework 5.2.3.RELEASE       | No untrusted Java deserialization (no HttpInvoker)              |

## Remaining Vulnerabilities (upgrade in production)

| Component               | Issue                 | Recommendation                                   |
|-------------------------|-----------------------|--------------------------------------------------|
| snakeyaml 1.25          | Multiple DoS/RCE CVEs | Upgrade ≥ 2.0                                    |
| jackson-databind 2.10.2 | XXE/DoS CVEs          | Upgrade ≥ 2.15                                   |
| bcprov-jdk15on 1.46     | Crypto weaknesses     | Upgrade ≥ 1.70 or remove if unused               |
| tomcat-embed 9.0.30     | Numerous CVEs         | Upgrade via newer Spring Boot (Tomcat ≥ 9.0.80+) |
| Spring Boot 2.2.4       | Multiple CVEs         | Upgrade to 2.7.x or 3.x                          |


## Before Suppression: 
<img width="1009" height="499" alt="Screenshot 2025-12-06 at 12 59 40 PM" src="https://github.com/user-attachments/assets/468d9658-6c53-4547-a1f3-69b50a28a0d6" />


## After Suppression: 
<img width="1021" height="720" alt="Screenshot 2025-12-06 at 12 59 17 PM" src="https://github.com/user-attachments/assets/975ebefd-c465-48b5-bcd0-ebfb55ffc143" />


## Verification

After adding suppressions:

```bash
mvn verify
```

Expect the dependency-check report to show the suppressed items as removed from the findings.

## Submission Checklist

- Updated `pom.xml` (includes suppressionFiles config)
- `suppression.xml` with suppress entries
- HTML reports: before and after suppression
- (If needed) screenshots of before/after reports
- Note in your write-up: the “after” report’s “Vulnerabilities Suppressed” section lists the suppressed CVEs, demonstrating the suppressions were applied.

## References

- OWASP Dependency-Check Suppression: https://jeremylong.github.io/DependencyCheck/general/suppression.html
- Spring4Shell analysis: https://spring.io/blog/2022/03/31/spring-framework-rce-early-announcement
- NVD: https://nvd.nist.gov/
