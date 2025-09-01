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
| `java-version`      | `21`                   | Java Version                                                     |
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
| `java-version`         | `21`             | Java Version                                                |
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
| `java-version`      | `21`                   | Java Version                                                     |
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
| `java-version`         | `21`             | Java Version                                                |
| `distribution`         | `corretto`       | Java Distribution                                           |
| `cache`                | `true`           | Enable Maven/Gradle dependency caching.                     |
| `cache-encryption-key` |                  | Optional encryption key for the Gradle configuration cache. |

#### Outputs

The following outputs are forwarded from the underlying `setup-java` and `cache` actions:

| Name    | Description                                                                 |
|---------|-----------------------------------------------------------------------------|
| `path`  | Path to where the java environment has been installed (same as $JAVA_HOME). |

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

To build and publish the chart, visit the GitHub Actions page of the repository and trigger the workflow "Release Package" manually.

## Information

About Bits is a company based in South Tyrol, Italy. You can find more information about us
on [our website](https://aboutbits.it).

### Support

For support, please contact [info@aboutbits.it](mailto:info@aboutbits.it).

### Credits

- [All Contributors](../../contributors)

### License

The MIT License (MIT). Please see the [license file](license.md) for more information.
