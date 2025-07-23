# GitHub Actions Java

A collection of GitHub actions for Java projects with Maven and Gradle support.

## Actions

### Setup Java

This action will set up the Java SDK.

#### Example:

```yaml
  # Maven
  - uses: aboutbits/github-actions-java/setup-with-maven@v4

  # Gradle
  - uses: aboutbits/github-actions-java/setup-with-gradle@v4
```

#### Inputs

The following inputs can be used as `step.with` keys:

| Name                | Required/Default       | Description                                                                                           |
|---------------------|------------------------|-------------------------------------------------------------------------------------------------------|
| `working-directory` | `.`                    | The working directory                                                                                 |
| `java-version`      | `21`                   | Java Version                                                                                          |
| `distribution`      | `corretto`             | Java Distribution                                                                                     |
| `cache`             | `true`                 | Enable Maven/Gradle dependency caching.                                                               |
| `cache-name`        | `aboutbits-setup-java` | Cache name. Caches with the same name will share their contents. (Not applicable when Gradle is used) |

#### Outputs

The following outputs are forwarded from the underlying `setup-java` and `cache` actions:

| Name           | Description                                                                                                    |
|----------------|----------------------------------------------------------------------------------------------------------------|
| `distribution` | Distribution of Java that has been installed.                                                                  |
| `version`      | Actual version of the java environment that has been installed.                                                |
| `path`         | Path to where the java environment has been installed (same as $JAVA_HOME).                                    |
| `cache-hit`    | A boolean value to indicate an exact match was found for the primary key. (Not applicable when Gradle is used) |

### Setup Java and Install Dependencies

This action will set up the Java SDK and install all dependencies.
You can choose between the Maven and Gradle build tool.

#### Example:

```yaml
  # Maven
  - uses: aboutbits/github-actions-java/setup-and-install-with-maven@v4

  # Gradle
  - uses: aboutbits/github-actions-java/setup-and-install-with-gradle@v4
```

#### Inputs

The following inputs can be used as `step.with` keys:

| Name                | Required/Default       | Description                                                                                           |
|---------------------|------------------------|-------------------------------------------------------------------------------------------------------|
| `working-directory` | `.`                    | The working directory                                                                                 |
| `java-version`      | `21`                   | Java Version                                                                                          |
| `distribution`      | `corretto`             | Java Distribution                                                                                     |
| `cache`             | `true`                 | Enable Maven/Gradle dependency caching.                                                               |
| `cache-name`        | `aboutbits-setup-java` | Cache name. Caches with the same name will share their contents. (Not applicable when Gradle is used) |

#### Outputs

The following outputs are forwarded from the underlying `setup-java` and `cache` actions:

| Name           | Description                                                                                                    |
|----------------|----------------------------------------------------------------------------------------------------------------|
| `distribution` | Distribution of Java that has been installed.                                                                  |
| `version`      | Actual version of the java environment that has been installed.                                                |
| `path`         | Path to where the java environment has been installed (same as $JAVA_HOME).                                    |
| `cache-hit`    | A boolean value to indicate an exact match was found for the primary key. (Not applicable when Gradle is used) |

## Versioning

In order to have a versioning in place and working, create lightweight tags that point to the appropriate minor release
versions.

Creating a new minor release:

```bash
git tag v4
git push --tags
```

Replacing an already existing minor release:

```bash
git tag -d v4
git push origin :refs/tags/v3
git tag v4
git push --tags
```

## Information

About Bits is a company based in South Tyrol, Italy. You can find more information about us
on [our website](https://aboutbits.it).

### Support

For support, please contact [info@aboutbits.it](mailto:info@aboutbits.it).

### Credits

- [All Contributors](../../contributors)

### License

The MIT License (MIT). Please see the [license file](license.md) for more information.
