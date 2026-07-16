# Project Overview

`org.apache.sling.jcr.jackrabbit.base` is an OSGi bundle providing Jackrabbit utility classes for Apache Sling. It bridges Jackrabbit configuration and security extension points with the OSGi service registry. Key components: `OsgiBeanFactory` (OSGi-aware Jackrabbit bean factory), `DelegatingLoginModule` (JAAS login delegation), `DelegatingPrincipalProviderRegistry`, `MultiplexingAuthorizableAction`, and `PrincipalProviderTracker`. No web layer — pure OSGi/JCR integration library.

# Core Commands

```bash
# Build and package
mvn clean install

# Compile only
mvn compile

# Run tests
mvn test

# Run a single test class
mvn test -Dtest=MyTestClass

# Skip tests (build only)
mvn install -DskipTests

# Format code (Spotless via google-java-format)
mvn spotless:apply

# Check formatting without modifying
mvn spotless:check

# Full verification (includes parent build checks)
mvn verify

# License header compliance
mvn apache-rat:check
```

# Project Layout

```text
pom.xml                          # Maven build descriptor; inherits sling-bundle-parent:66
src/
  main/
    java/
      org/apache/sling/jcr/jackrabbit/base/
        config/
          OsgiBeanFactory.java                    # OSGi-aware BeanFactory for repository config
        security/
          DelegatingLoginModule.java             # Delegates JAAS login to OSGi or Jackrabbit fallback
          DelegatingPrincipalProviderRegistry.java
          MultiplexingAuthorizableAction.java    # Fans out authorizable actions to OSGi services
          PrincipalProviderTracker.java          # Tracks OSGi principal provider services
          package-info.java                      # Package-level OSGi versioning annotation
target/                          # Build output; not committed
```

No `src/test/` directory currently exists.

# Development Patterns & Constraints

- **Java version**: Java 8 source compatibility (`sling.java.version=8`). Do not use Java 9+ APIs.
- **Code style**: Google Java Format (enforced by Spotless via the parent POM). Run `mvn spotless:apply` before committing.
- **Indentation**: 2 spaces (Google style).
- **OSGi integration style**: This module uses `BundleContext`/`ServiceTracker` APIs directly for service tracking and registration (not Declarative Services components).
- **Imports**: No wildcard imports. Static imports only for constants/utilities where idiomatic.
- **Logging**: SLF4J only (`org.slf4j.Logger`/`LoggerFactory`).
- **License headers**: Every `.java` file must carry the Apache 2.0 license header. RAT check (`mvn apache-rat:check`) enforces this.
- **Dependencies**: Runtime dependencies are `provided` (container-supplied); test-only dependencies use `test` scope.
- **Animal Sniffer**: `animal-sniffer-maven-plugin` enforces Java 8 API compatibility.
- **OSGi versioning**: Use `@org.osgi.annotation.versioning` on exported packages (`package-info.java`).

# Git Workflow

- Mirrors Apache Sling conventions: [https://sling.apache.org/contributing.html](https://sling.apache.org/contributing.html)
- Main branch: `master`
- Commit messages: short imperative summary (≤72 chars), reference Jira issue where applicable (e.g., `SLING-12345 Fix DelegatingLoginModule NPE`).
- Contributions via GitHub PRs against this repo; CI runs via Jenkins (`slingOsgiBundleBuild()` pipeline function).
- Do not push directly to `master` without review.

# Testing Guidelines

- Framework: JUnit 4 (`junit:junit`, test scope).
- Test logging backend: `org.slf4j:slf4j-simple` (test scope).
- Test files go in `src/test/java/` mirroring the main package structure.
- Run all tests: `mvn test`
- Run one test: `mvn test -Dtest=ClassName` or `mvn test -Dtest=ClassName#methodName`
- No coverage tooling configured by default; add JaCoCo if needed per task.
- The bundle currently has no unit tests — adding them is welcome.

# Gotchas

- **No OSGi runtime in tests**: There is no embedded OSGi framework for tests. Mock `BundleContext` and related OSGi interfaces manually or with Mockito.
- **Jackrabbit 2.x, not Oak**: This bundle targets `jackrabbit-core:2.5.2` (Jackrabbit 2, not Apache Jackrabbit Oak). APIs differ substantially from Oak.
- **Spotless fail on CI**: Formatting is checked during `verify`. Always run `mvn spotless:apply` before pushing.
- **RAT check**: Missing or malformed license headers fail the build. Any new file needs the ASF license block.
- **`bnd.baseline.fail.on.missing=false`**: OSGi semantic versioning baseline checking is relaxed when a baseline artifact is missing.
- **Animal Sniffer**: Importing any API added after Java 8 (for example `java.util.Optional.ifPresentOrElse`) fails the build in the sniffer phase.

# Security

<!-- sling-security-default:start -->
The threat model for this project is https://github.com/apache/sling/blob/master/docs/threat-model.md .
<!-- sling-security-default:end -->
