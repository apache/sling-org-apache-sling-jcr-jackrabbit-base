# Project Overview

`org.apache.sling.jcr.jackrabbit.base` is an OSGi bundle providing Jackrabbit utility classes for Apache Sling. It bridges Jackrabbit's internal configuration and security APIs with the OSGi service registry. Key components: `OsgiBeanFactory` (OSGi-aware Jackrabbit bean factory), `DelegatingLoginModule` (JAAS login delegation), `DelegatingPrincipalProviderRegistry`, `MultiplexingAuthorizableAction`, and `PrincipalProviderTracker`. No web layer — pure OSGi/JCR integration library.

# Core Commands

```bash
# Build and package
mvn clean install

# Compile only
mvn compile

# Run full test suite
mvn test

# Run a single test class
mvn test -Dtest=MyTestClass

# Skip tests (build only)
mvn install -DskipTests

# Format code (Spotless via google-java-format)
mvn spotless:apply

# Check formatting without modifying
mvn spotless:check

# Verify OSGi baseline compliance
mvn verify
```

# Project Layout

```
pom.xml                          # Maven build descriptor; inherits sling-bundle-parent:66
src/
  main/
    java/
      org/apache/sling/jcr/jackrabbit/base/
        config/
          OsgiBeanFactory.java   # OSGi-aware BeanFactory for Jackrabbit repository config
        security/
          DelegatingLoginModule.java             # Delegates JAAS login to OSGi-registered modules
          DelegatingPrincipalProviderRegistry.java
          MultiplexingAuthorizableAction.java    # Fans out authorizable actions to OSGi services
          PrincipalProviderTracker.java          # Tracks OSGi principal provider services
          package-info.java                      # Package-level OSGi versioning annotation
target/                          # Build output; not committed
```

No `src/test/` directory — currently no tests.

# Development Patterns & Constraints

- **Java version**: Java 8 source compatibility (`sling.java.version=8`). Do not use Java 9+ APIs.
- **Code style**: Google Java Format (enforced by Spotless, inherited from `sling-bundle-parent`). Run `mvn spotless:apply` before committing.
- **Indentation**: 2 spaces (Google style).
- **OSGi**: OSGi R6/R7 annotations (`org.osgi.service.component.annotations`). Do not use Felix SCR annotations.
- **Imports**: No wildcard imports. Static imports only for constants/utilities where idiomatic.
- **Logging**: SLF4J only (`org.slf4j.Logger`/`LoggerFactory`). No `java.util.logging` or Log4j direct usage.
- **License headers**: Every `.java` file must carry the Apache 2.0 license header. RAT check (`mvn apache-rat:check`) enforces this.
- **Dependencies**: All dependencies declared `provided` (OSGi container supplies them at runtime) or `test`. Do not add `compile`-scope runtime deps without discussion.
- **Animal Sniffer**: `animal-sniffer-maven-plugin` checks Java 8 API compliance on every build.
- **OSGi versioning**: Use `@org.osgi.annotation.versioning` on exported packages (`package-info.java`).

# Git Workflow

- Mirrors Apache Sling conventions: [https://sling.apache.org/contributing.html](https://sling.apache.org/contributing.html)
- Main branch: `master`
- Commit messages: start with a short imperative summary (≤72 chars), reference Jira issue where applicable (e.g., `SLING-12345 Fix DelegatingLoginModule NPE`).
- Contributions via GitHub PRs against this repo; CI runs via Jenkins (`slingOsgiBundleBuild()` pipeline function).
- Do not push directly to `master` without review.

# Testing Guidelines

- Framework: JUnit 4 (`junit:junit`, test scope).
- Test files go in `src/test/java/` mirroring the main package structure.
- Run all tests: `mvn test`
- Run one test: `mvn test -Dtest=ClassName` or `mvn test -Dtest=ClassName#methodName`
- No coverage tooling configured by default; add JaCoCo if needed per task.
- The bundle currently has no unit tests — adding them is welcome.

# Gotchas

- **No OSGi runtime in tests**: There is no embedded OSGi framework for tests. Mock `BundleContext` and related OSGi interfaces manually or with Mockito.
- **Jackrabbit 2.x, not Oak**: This bundle targets `jackrabbit-core:2.5.2` (Jackrabbit 2, not Apache Jackrabbit Oak). APIs differ substantially from Oak.
- **Spotless fail on CI**: Formatting is checked during `verify`. Always run `mvn spotless:apply` before pushing — raw format mismatches will break the Jenkins build.
- **RAT check**: Missing or malformed license headers fail the build. Any new file needs the ASF license block.
- **`bnd.baseline.fail.on.missing=false`**: OSGi semantic versioning baseline check is relaxed (no baseline artifact yet). Enable carefully when releasing.
- **Animal Sniffer**: Importing any API added after Java 8 (e.g., `java.util.Optional.ifPresentOrElse`) will fail the build silently at the sniffer phase — check carefully when upgrading utilities.

# Security

<!-- sling-security-default:start -->
The threat model for this project is https://github.com/apache/sling/blob/master/docs/threat-model.md .
<!-- sling-security-default:end -->

