# Static Testing

- Using the code base in Supporting Materials, edit `pom.xml` to integrate the
  Maven dependency-check plug-in. The Integrating the Maven Dependency-Check
  Plug-In Tutorial in Supporting Materials may help.
- Run a dependency check, document known vulnerabilities, and submit the HTML
  dependency-check report with the findings.
- Expect some false positives; understand them even though fixes are not
  available.

# Reconfiguration (suppressing false positives)

Sometimes you must live with an error until a fix exists. Reconfigure
dependency-check to suppress false positives by creating `suppression.xml` and
updating `pom.xml` so the false positives no longer appear in the report (they
still exist, but are hidden).

1. Open the dependency-check HTML report in a browser.
2. Click the suppress button next to each false-positive vulnerability.
3. Click **Complete XML Doc** and copy the highlighted contents.
4. In Eclipse, create `suppression.xml` in the same directory as `pom.xml`.
5. Paste the copied XML into `suppression.xml`.
6. Edit `pom.xml` and, inside the OWASP dependency-check configuration, add:

```xml
<suppressionFiles>
  <suppressionFile>suppression.xml</suppressionFile>
</suppressionFiles>
```

# Verification and Submission

- Run dependency-check again (Maven Run As) to confirm all dependencies are
  valid and no false positives remain.
- Submit the updated HTML dependency-check report showing no false positives.
- Zip the Eclipse project folder and submit it, including `suppression.xml` and
  the revised `pom.xml`.
