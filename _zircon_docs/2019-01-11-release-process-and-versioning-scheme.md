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

`YYYY.r.p` (year, release, preview)

Every release is prefixed with a *year*. After the *year* there is a release number which is a
monotonous increasing number (e.g. `1, 2, 3, n`). With every *year* this number resets and 
starts over from `1`.

`p` is a preview release which is either a **bugfix**, or a **preview** of a new feature.
The same rules apply to `p` which apply to `r` (see above).

## Releases

A major release (`r`) goes to Maven (like [this](https://mvnrepository.com/artifact/org.codetome.zircon/zircon/2017.3.1) one),
while a preview release is just a *tag* on github and it is postfixed with `-PREVIEW`
(like [this](https://github.com/Hexworks/zircon/releases/tag/2018.3.14-PREVIEW) one).

> Note that due to an outside problem which will be fixed soon currently major releases
are also accessible on [Jitpack](https://jitpack.io/#org.hexworks/zircon).

`PREVIEW` releases reside on [Jitpack](https://jitpack.io/#org.hexworks/zircon).

**Note that `PREVIEW` releases may contain previews of features which are not complete and/or
APIs which might change in a major release so bear this in mind.** We suggest that you should
only use `PREVIEW` releases if you want to try out a feature.

## `PREVIEW` releases

`PREVIEW`s are closely associated with our [project Board](https://github.com/Hexworks/zircon/projects/2): 
from every completed task which is either an *enhancement*, a *bug* or a *feature* you can expect a `PREVIEW` to be created.

Check the [GitHub releases](https://github.com/Hexworks/zircon/releases) page to see which `PREVIEW`s are available.

An example Jitpack dependency:

Gradle:
```groovy
implementation 'org.hexworks.zircon:zircon.core-jvm:2019.1.2-PREVIEW'
implementation 'org.hexworks.zircon:zircon.jvm.swing:2019.1.2-PREVIEW'
```

Maven:

```xml
<dependency>
    <groupId>org.hexworks.zircon</groupId>
    <artifactId>zircon.core-jvm</artifactId>
    <version>2019.1.2-PREVIEW</version>
</dependency>
<dependency>
    <groupId>org.hexworks.zircon</groupId>
    <artifactId>zircon.jvm.swing</artifactId>
    <version>2019.1.2-PREVIEW</version>
</dependency>
```

In order to use Jitpack `PREVIEW`s you also need to add the Jitpack repository to your project:

Gradle:
```groovy
repositories {
    maven { url 'https://jitpack.io' }
}
```

Maven:
```xml
<repositories>
    <repository>
        <id>jitpack.io</id>
        <url>https://jitpack.io</url>
    </repository>
</repositories>
```

{% include zircon_links.md %}
