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

GitHub compares new advisories with the last submitted dependency graph, so the workflow must run only after the dependencies change. The example runs on a push to `main` that changes a file that can change the resolved dependencies or the Gradle version. Use the manual trigger to submit the graph at once, for example after a new release of this action.

```yaml
name: Dependency Submission

on:
  push:
    branches:
      - main
    paths:
      - '**/*.gradle'
      - '**/*.gradle.kts'
      - '**/gradle.properties'
      - '**/*.versions.toml'
      - '**/*.lockfile'
      - 'buildSrc/**'
      - 'gradle/wrapper/gradle-wrapper.properties'
      - '.github/workflows/dependency-submission.yml'
  workflow_dispatch:

env:
  JAVA_VERSION: 25

concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true

permissions:
  contents: write

jobs:
  dependency-submission:
    runs-on: ubuntu-26.04
    timeout-minutes: 10
    steps:
      - uses: actions/checkout@v7
        with:
          persist-credentials: false
      - uses: aboutbits/github-actions-java/dependency-submission-with-gradle@v4
        with:
          java-version: ${{ env.JAVA_VERSION }}
          project-type: spring-boot
```

The paths list covers `buildSrc`. When `settings.gradle.kts` includes another build with `includeBuild`, add the sources of that build too, for example `'build-logic/**'`: a convention plugin class can change the resolved dependencies. When the build is in a `working-directory`, put that folder in front of the Gradle wrapper path.

The action runs Gradle in its own step. When the build needs credentials, for example for GitHub Packages, set the variables that your build reads in the `env` of the action step, not of the job. The step environment reaches the Gradle run, and the other steps do not get the credentials. GitHub Packages also needs the `packages: read` permission.

The token in this job has `contents: write`. Every build script and Gradle plugin that the dependency resolution runs can read the variables of the step. So prefer a token that can only read packages, for example a secret with a token that has only the `read:packages` scope. With `secrets.GITHUB_TOKEN`, the build gets the write permission of the job.

```yaml
permissions:
  contents: write
  packages: read

# ...

      - uses: aboutbits/github-actions-java/dependency-submission-with-gradle@v4
        with:
          java-version: ${{ env.JAVA_VERSION }}
          project-type: spring-boot
        env:
          GITHUB_USER_NAME: ${{ github.actor }}
          GITHUB_ACCESS_TOKEN: ${{ secrets.GITHUB_TOKEN }}
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

| Project type  | Excluded configurations   | Runtime configurations                                       |
|---------------|---------------------------|--------------------------------------------------------------|
| `spring-boot` | `detachedConfiguration.*` | `productionRuntimeClasspath`                                 |
| `quarkus`     | None                      | `runtimeClasspath\|quarkusProdRuntimeClasspathConfiguration` |

- `spring-boot`: The Spring dependency management plugin resolves detached copies of each configuration. These copies can contain other versions than the real classpath, for example the version from the Spring Boot BOM. Without the filter, Dependabot reports alerts for versions that the application does not use.
- `spring-boot`: Only `productionRuntimeClasspath`, the classpath of the boot JAR, gives the scope "runtime". The Spring Boot plugin adds `developmentOnly` and `testAndDevelopmentOnly` to `runtimeClasspath`, so those dependencies get the scope "development". A module without the Spring Boot plugin has no `productionRuntimeClasspath`; its dependencies get the scope "runtime" when the application module depends on it.
- `quarkus`: The action uses no filter. Dependencies that only the build-time configurations of Quarkus resolve, for example the deployment modules of the extensions, get the scope "development".

#### Included build-logic builds

The dependency graph also contains the builds that `settings.gradle.kts` includes with `includeBuild`, for example a build with a custom Gradle convention plugin for the jOOQ code generation. The runtime configurations match by name in every project of the graph.

- `spring-boot`: No `productionRuntimeClasspath` of the application contains a build-logic build, so its dependencies get the scope "development" without more settings.
- `quarkus`, or your own `runtime-configurations` that a build-logic build also has, for example `runtimeClasspath`: the dependencies of that build get the scope "runtime", although the application does not contain them. Set `runtime-exclude-projects` to the project paths of that build. The dependencies stay in the dependency graph with the scope "development".

```yaml
      - uses: aboutbits/github-actions-java/dependency-submission-with-gradle@v4
        with:
          project-type: quarkus
          runtime-exclude-projects: ':build-logic(:.*)?'
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
