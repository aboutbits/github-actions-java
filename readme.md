# GitHub Actions Java

A collection of GitHub actions for Java projects.

## Setup Java and Update Dependencies

This action will set up the Java SDK and initialize the cache for Maven dependencies. It will then install or update all dependencies.

### Example

```yaml
  - name: Checkout source code
    uses: actions/checkout@v3

  - uses: aboutbits/github-actions-java/setup-and-update-dependencies@v1
```

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
