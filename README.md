[![Apache Sling](https://sling.apache.org/res/logos/sling.png)](https://sling.apache.org)

&#32;[![Build Status](https://ci-builds.apache.org/job/Sling/job/modules/job/sling-org-apache-sling-jcr-jackrabbit-base/job/master/badge/icon)](https://ci-builds.apache.org/job/Sling/job/modules/job/sling-org-apache-sling-jcr-jackrabbit-base/job/master/)&#32;[![Coverage](https://sonarcloud.io/api/project_badges/measure?project=apache_sling-org-apache-sling-jcr-jackrabbit-base&metric=coverage)](https://sonarcloud.io/dashboard?id=apache_sling-org-apache-sling-jcr-jackrabbit-base)&#32;[![Sonarcloud Status](https://sonarcloud.io/api/project_badges/measure?project=apache_sling-org-apache-sling-jcr-jackrabbit-base&metric=alert_status)](https://sonarcloud.io/dashboard?id=apache_sling-org-apache-sling-jcr-jackrabbit-base)&#32;[![jcr](https://sling.apache.org/badges/group-jcr.svg)](https://github.com/apache/sling-aggregator/blob/master/docs/groups/jcr.md) [![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://www.apache.org/licenses/LICENSE-2.0)

# Apache Sling JCR Jackrabbit Base

This module is part of the [Apache Sling](https://sling.apache.org) project.

The JCR base bundle provides Jackrabbit utility classes for integrating Jackrabbit
security and configuration extension points with an OSGi runtime.

## Key Components

* `OsgiBeanFactory` (`org.apache.sling.jcr.jackrabbit.base.config`) tracks OSGi
  services marked as Jackrabbit extensions and exposes a `BeanFactory` when all
  required dependencies are available.
* `DelegatingLoginModule` delegates JAAS login handling to an OSGi-based JAAS
  configuration when available, with fallback to Jackrabbit's local module setup.
* `PrincipalProviderTracker` and `DelegatingPrincipalProviderRegistry` bridge
  OSGi-registered `PrincipalProvider` services into Jackrabbit's principal
  provider registry model.
* `MultiplexingAuthorizableAction` tracks OSGi `AuthorizableAction` services and
  dispatches Jackrabbit authorizable lifecycle callbacks to them.

## Build

```bash
mvn clean install
```

## Common Commands

```bash
# Compile only
mvn compile

# Run tests
mvn test

# Check formatting
mvn spotless:check

# Apply formatting
mvn spotless:apply

# Full verification (includes integration checks from parent build)
mvn verify
```

## Requirements and Dependencies

* Java 8 (`sling.java.version=8`)
* Apache Jackrabbit Core `2.5.2` (provided)
* JCR API (`javax.jcr:jcr`, provided)
* OSGi APIs (`org.osgi.framework`, `org.osgi.util.tracker`,
  `org.osgi.annotation.versioning`, provided)
* SLF4J API (provided)
* JUnit 4 and `slf4j-simple` for tests

## Source Layout

```text
src/main/java/org/apache/sling/jcr/jackrabbit/base/
  config/
    OsgiBeanFactory.java
  security/
    DelegatingLoginModule.java
    DelegatingPrincipalProviderRegistry.java
    MultiplexingAuthorizableAction.java
    PrincipalProviderTracker.java
```
