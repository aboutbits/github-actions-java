# GitHub Actions Java

A collection of GitHub actions for Java projects.

## Actions

### Setup Java and Install Dependencies

This action will set up the Java SDK and install all dependencies.

#### Example:

```yaml
  - uses: aboutbits/github-actions-java/setup-and-install@v1
```

#### Inputs

The following inputs can be used as `step.with` keys:

| Name                   | Required/Default | Description               |
|------------------------|------------------|---------------------------|
| `working-directory`    | `.`              | The working directory     |
| `java-version`         | `17`             | Java Version              |
| `distribution`         | `corretto`       | Java Distribution         |


## Versioning

In order to have a versioning in place and working, create lightweight tags that point to the appropriate minor release versions.

Creating a new minor release:

```bash
git tag v1
git push --tags
```

Replacing an already existing minor release:

```bash
git tag -d v1
git push origin :refs/tags/v1
git tag v1
git push --tags
```

## Information

About Bits is a company based in South Tyrol, Italy. You can find more information about us on [our website](https://aboutbits.it).

### Support

For support, please contact [info@aboutbits.it](mailto:info@aboutbits.it).

### Credits

- [All Contributors](../../contributors)

### License

The MIT License (MIT). Please see the [license file](license.md) for more information.
