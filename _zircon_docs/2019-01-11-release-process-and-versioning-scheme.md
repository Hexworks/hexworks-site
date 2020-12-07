---
excerpt: Zircon follows a versioning scheme which is reminiscent to JetBrains' versioning strategy but with a twist.
title: Release Process and Versioning Scheme
tags: [zircon, documentation, zircon-documentation]
author: hexworks
short_title: Release Process and Versioning Scheme
---

## Versioning

Zircon follows a versioning scheme which is reminiscent to JetBrains' versioning strategy but 
with a twist:

`YYYY.major.minor-kind`

Every release is prefixed with a *year*. After the *year* there is a release number which is a
monotonous increasing number (e.g. `1, 2, 3, n`). With every *year* this number resets and 
starts over from `1`.

`minor` is either a **hotfix**, or a **preview** of a new feature.
The same rules apply to `minor` which apply to `major` (see above).

## Releases

Every release goes to Maven Central (like [this](https://search.maven.org/search?q=a:zircon.core-jvm) one).

Releases are postfixed with the `kind` of the release: `RELEASE`, `HOTFIX` or `PREVIEW`.

> Note that `PREVIEW` releases may contain previews of features which are not complete and/or
> APIs which might change in a major release so bear this in mind.

## `PREVIEW` releases

`PREVIEW`s are closely associated with our [project Board](https://github.com/Hexworks/zircon/projects/2): 
from every completed task which is either an *enhancement*, a *bug* or a *feature* you can expect a `PREVIEW` to be created.

Check the [GitHub releases](https://github.com/Hexworks/zircon/releases) page to see which `PREVIEW`s are available.

To use any release in your project you can either download the artifacts from *Maven Central* or load them using *Maven* or *Gradle*.

## Adding Zircon to Your Project

In order to get *Zircon* working in your project you have to add a **core** package (we only have `jvm` for now) and a **target** package:

Maven:

```xml
<dependencies>
    <!-- Zircon core package for the jvm. This is always needed. -->
    <dependency>
        <groupId>org.hexworks.zircon</groupId>
        <artifactId>zircon.core-jvm</artifactId>
        <version>2020.2.0-RELEASE</version>
    </dependency>
    <!-- Zircon target package. You can use either zircon.jvm.swing or zircon.jvm.libgdx -->
    <dependency>
        <groupId>org.hexworks.zircon</groupId>
        <artifactId>zircon.jvm.swing</artifactId>
        <version>2020.2.0-RELEASE</version>
    </dependency>
</dependencies>
```

Gradle:

```groovy
dependencies {
    implementation "org.hexworks.zircon:zircon.core-jvm:2020.2.0-RELEASE"
    implementation "org.hexworks.zircon:zircon.jvm.swing:2020.2.0-RELEASE"
}
```


{% include zircon_links.md %}
