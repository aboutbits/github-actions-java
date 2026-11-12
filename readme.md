# GitHub Actions Java

A collection of GitHub actions for Java projects with Maven and Gradle support.

## Actions

### Setup Java with Maven

This action will set up the Java SDK including Maven.

#### Example:

```yaml
  - uses: aboutbits/github-actions-java/setup-with-maven@v4
```

#### Inputs

The following inputs can be used as `step.with` keys:

| Name                | Required/Default       | Description                                                      |
|---------------------|------------------------|------------------------------------------------------------------|
| `working-directory` | `.`                    | The working directory                                            |
| `java-version`      | `25`                   | Java Version                                                     |
| `distribution`      | `corretto`             | Java Distribution                                                |
| `cache`             | `true`                 | Enable Maven/Gradle dependency caching.                          |
| `cache-name`        | `aboutbits-setup-java` | Cache name. Caches with the same name will share their contents. |

#### Outputs

The following outputs are forwarded from the underlying `setup-java` and `cache` actions:

| Name   | Description                                                                 |
|--------|-----------------------------------------------------------------------------|
| `path` | Path to where the java environment has been installed (same as $JAVA_HOME). |


### Setup Java with Gradle

This action will set up the Java SDK including Gradle.

#### Example:

```yaml
  - uses: aboutbits/github-actions-java/setup-with-gradle@v4
```

#### Inputs

The following inputs can be used as `step.with` keys:

| Name                   | Required/Default | Description                                                 |
|------------------------|------------------|-------------------------------------------------------------|
| `working-directory`    | `.`              | The working directory                                       |
| `java-version`         | `25`             | Java Version                                                |
| `distribution`         | `corretto`       | Java Distribution                                           |
| `cache`                | `true`           | Enable Maven/Gradle dependency caching.                     |
| `cache-encryption-key` |                  | Optional encryption key for the Gradle configuration cache. |

#### Outputs

The following outputs are forwarded from the underlying `setup-java` and `cache` actions:

| Name   | Description                                                                 |
|--------|-----------------------------------------------------------------------------|
| `path` | Path to where the java environment has been installed (same as $JAVA_HOME). |

### Setup Java and Install Dependencies with Maven

This action will set up the Java SDK and install all dependencies using Maven.

#### Example:

```yaml
  - uses: aboutbits/github-actions-java/setup-and-install-with-maven@v4
```

#### Inputs

The following inputs can be used as `step.with` keys:

| Name                | Required/Default       | Description                                                      |
|---------------------|------------------------|------------------------------------------------------------------|
| `working-directory` | `.`                    | The working directory                                            |
| `java-version`      | `25`                   | Java Version                                                     |
| `distribution`      | `corretto`             | Java Distribution                                                |
| `cache`             | `true`                 | Enable Maven/Gradle dependency caching.                          |
| `cache-name`        | `aboutbits-setup-java` | Cache name. Caches with the same name will share their contents. |

#### Outputs

The following outputs are forwarded from the underlying `setup-java` and `cache` actions:

| Name   | Description                                                                 |
|--------|-----------------------------------------------------------------------------|
| `path` | Path to where the java environment has been installed (same as $JAVA_HOME). |

### Setup Java and Install Dependencies with Gradle

This action will set up the Java SDK and install all dependencies using Gradle.

#### Example:

```yaml
  - uses: aboutbits/github-actions-java/setup-and-install-with-gradle@v4
```

#### Inputs

The following inputs can be used as `step.with` keys:

| Name                   | Required/Default | Description                                                 |
|------------------------|------------------|-------------------------------------------------------------|
| `working-directory`    | `.`              | The working directory                                       |
| `java-version`         | `25`             | Java Version                                                |
| `distribution`         | `corretto`       | Java Distribution                                           |
| `cache`                | `true`           | Enable Maven/Gradle dependency caching.                     |
| `cache-encryption-key` |                  | Optional encryption key for the Gradle configuration cache. |

#### Outputs

The following outputs are forwarded from the underlying `setup-java` and `cache` actions:

| Name    | Description                                                                 |
|---------|-----------------------------------------------------------------------------|
| `path`  | Path to where the java environment has been installed (same as $JAVA_HOME). |

### Submit Dependency Graph with Gradle

This action will set up the Java SDK, generate the dependency graph of a Gradle build and submit it to GitHub. GitHub uses the dependency graph for Dependabot alerts, also for transitive dependencies.

The action resolves all dependencies of the build. It does not build or test the project.

#### Example:

The workflow must have the `contents: write` permission to submit the dependency graph.

GitHub compares new advisories with the last submitted dependency graph, so the workflow must run only after the dependencies change. The example runs once on each weekday morning. For a project in maintenance mode, a weekly run is enough, for example `'17 5 * * 1'`. Use the manual trigger to submit the graph at once, for example after you add the workflow or update a vulnerable dependency.

```yaml
name: Dependency Submission

on:
  schedule:
    # 05:17 UTC on weekdays. An odd minute, because GitHub can delay runs at the full hour.
    - cron: '17 5 * * 1-5'
  workflow_dispatch:

concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true

permissions:
  contents: write

jobs:
  dependency-submission:
    runs-on: ubuntu-26.04
    steps:
      - uses: actions/checkout@v4
      - uses: aboutbits/github-actions-java/dependency-submission-with-gradle@v4
        with:
          project-type: spring-boot
```

#### Inputs

The following inputs can be used as `step.with` keys:

| Name                       | Required/Default | Description                                                                              |
|----------------------------|------------------|------------------------------------------------------------------------------------------|
| `working-directory`        | `.`              | The working directory                                                                    |
| `java-version`             | `25`             | Java Version                                                                             |
| `distribution`             | `corretto`       | Java Distribution                                                                        |
| `project-type`             | Required         | `spring-boot` or `quarkus`. Selects the configuration filter and runtime scope.          |
| `runtime-configurations`   |                  | Optional regex that replaces the runtime configurations of the project type.             |
| `runtime-exclude-projects` |                  | Optional regex of Gradle project paths whose dependencies never get the scope "runtime". |

#### Project types

The project type sets which Gradle configurations go into the dependency graph, and which dependencies get the scope "runtime". All other dependencies get the scope "development".

| Project type  | Excluded configurations    | Runtime configurations                                     |
|---------------|----------------------------|------------------------------------------------------------|
| `spring-boot` | `detachedConfiguration.*`  | `runtimeClasspath\|productionRuntimeClasspath`              |
| `quarkus`     | None                       | `runtimeClasspath\|quarkusProdRuntimeClasspathConfiguration` |

- `spring-boot`: The Spring dependency management plugin resolves detached copies of each configuration. These copies can contain other versions than the real classpath, for example the version from the Spring Boot BOM. Without the filter, Dependabot reports alerts for versions that the application does not use.
- `quarkus`: The action uses no filter. Dependencies that only the build-time configurations of Quarkus resolve, for example the deployment modules of the extensions, get the scope "development".

#### Included build-logic builds

The dependency graph also contains the builds that `settings.gradle.kts` includes with `includeBuild`, for example a build with a custom Gradle convention plugin for the jOOQ code generation. The runtime configurations match by name in every project, so the dependencies of such a build get the scope "runtime", although the application does not contain them. Set `runtime-exclude-projects` to the project paths of that build. The dependencies stay in the dependency graph with the scope "development".

```yaml
      - uses: aboutbits/github-actions-java/dependency-submission-with-gradle@v4
        with:
          project-type: spring-boot
          runtime-exclude-projects: ':jooq(:.*)?'
```

### Set Version with Maven

This action will set the given version in a pom.xml file.

#### Example:

```yaml
  - uses: aboutbits/github-actions-java/set-version-with-maven@v4
```

#### Inputs

The following inputs can be used as `step.with` keys:

| Name                   | Required/Default | Description           |
|------------------------|------------------|-----------------------|
| `working-directory`    | `.`              | The working directory |
| `version`              | Required         | Package version       |

## Gradle cache configuration

To enable the Gradle configuration cache, which further improves the build performance in addition to the default enabled wrapper/script/dependency/build cache, pass a secret to the `cache-encryption-key` input.
If you do not specify `cache-encryption-key`, Gradle will still work, but the configuration cache will not be saved.
https://github.com/gradle/actions/blob/main/docs/setup-gradle.md#saving-configuration-cache-data

## Build & Publish

To build and publish the action, visit the GitHub Actions page of the repository and trigger the workflow "Release Package" manually.

## Information

About Bits is a company based in South Tyrol, Italy. You can find more information about us
on [our website](https://aboutbits.it).

### Support

For support, please contact [info@aboutbits.it](mailto:info@aboutbits.it).

### Credits

- [All Contributors](../../contributors)

### License

The MIT License (MIT). Please see the [license file](license.md) for more information.
