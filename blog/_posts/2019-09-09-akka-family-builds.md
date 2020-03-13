---
layout: post
title: Akka family build infrastructure
author: Martynas Mickevičius
category: article
short: An overview of sbt plugins and techniques used in Akka family build configuration
---

Hello hakkers,

this blog post is an overview of some sbt configurations used across [Alpakka](https://github.com/akka/alpakka), [Alpakka Kafka](https://github.com/akka/alpakka-kafka) and other Akka family projects.
The emphasis here will be made on tools that help us to provide a coherent experience in the code and the documentation.

## Code formatting

The sbt community has multiple sbt plugins which can be used to make sure that various parts of the project are always kept under the agreed standards.
When code formatting is automated, all of the discussions regarding code formatting can be diverted to discussions on code formatter configuration.
Akka family projects use the following plugins in concert to allow contributors to spend as much time focusing on the code.

### Scala and sbt code

Scala and sbt code is formatted using the [`sbt-scalafmt`](https://github.com/scalameta/sbt-scalafmt) plugin.

```scala
addSbtPlugin("org.scalameta" % "sbt-scalafmt" % "2.0.4")
```
<sup>Example in [Alpakka repository](https://github.com/akka/alpakka/blob/d0b0dde195407a0ec2c95447ac198f40d4bf502c/project/plugins.sbt#L2)</sup>

The plugin uses [`scalafmt`](https://github.com/scalameta/scalafmt) tool to do the formatting. `scalafmt` configuration is stored in the [`.scalafmt.conf`](https://github.com/akka/alpakka/blob/d0b0dde195407a0ec2c95447ac198f40d4bf502c/.scalafmt.conf) file.
To make sure that all code is formatted according to the configuration, reformatting on every compile is turned on in `sbt-scalafmt` configuration.

```scala
scalafmtOnCompile := true
```
<sup>Example in [Alpakka repository](https://github.com/akka/alpakka/blob/d0b0dde195407a0ec2c95447ac198f40d4bf502c/project/Common.scala#L114)</sup>

Scalafmt's formatting is a bit slow and does not work well in larger projects. This is why Akka relies on the user to format code correctly, which can be assisted by [IntelliJ's "Reformat on file save"](https://github.com/akka/akka/blob/master/CONTRIBUTING.md#scala-style). Alternatively formatting can be triggered with `scalafmtAll` (and `scalafmtSbt` for the sbt build files).

However some unformatted code might still slip in. Therefore during PR validation Travis CI is configured to check all of the code according to the formatting rules.

```yaml
jobs:
  include:
    - stage: check
      script: sbt scalafmtCheckAll || { echo "[error] Unformatted code found. Please run 'scalafmtAll' and commit the reformatted code."; false; }
      name: "Code style check (fixed with `sbt scalafmtAll`)"
    - script: sbt scalafmtSbtCheck || { echo "[error] Unformatted sbt code found. Please run 'scalafmtSbt' and commit the reformatted code."; false; }
      name: "Build code style check (fixed with `sbt scalafmtSbt`)"
```
<sup>Example in [Alpakka repository](https://github.com/akka/alpakka/blob/d0b0dde195407a0ec2c95447ac198f40d4bf502c/.travis.yml#L19-L23)</sup>

### Java code

Java code is formatted using the [`sbt-java-formatter`](https://github.com/sbt/sbt-java-formatter) plugin.

```scala
addSbtPlugin("com.lightbend.sbt" % "sbt-java-formatter" % "0.4.4")
```
<sup>Example in [Alpakka repository](https://github.com/akka/alpakka/blob/d0b0dde195407a0ec2c95447ac198f40d4bf502c/project/plugins.sbt#L15)</sup>

This plugin uses the [`google-java-format`](https://github.com/google/google-java-format) tool to reformat Java source code files.
The tool does not support configuration and reformats the code according to the predefined rules.

### Source code file headers

The [`sbt-header`](https://github.com/sbt/sbt-header) plugin is used to make sure that all of the source files have an up to date copyright notice information at the beginning of the files. 

```scala
addSbtPlugin("de.heikoseeberger" % "sbt-header" % "5.2.0")
```
<sup>Example in [Alpakka repository](https://github.com/akka/alpakka/blob/d0b0dde195407a0ec2c95447ac198f40d4bf502c/project/plugins.sbt#L1)</sup>

Header information is kept up to date by [enabling `AutomateHeaderPlugin`](https://github.com/akka/alpakka/blob/d0b0dde195407a0ec2c95447ac198f40d4bf502c/build.sbt#L371) which then applies updates if needed to every file on compile.

## Documentation

Project documentation usually consists of two parts: API docs generated from the source files and reference docs written separately to give context to the API docs.

### API documentation

The `sbt-unidoc` plugin is used to pull API documentation from all of the modules in the project to one single site.

```scala
addSbtPlugin("com.eed3si9n" % "sbt-unidoc" % "0.4.2")
```
<sup>Example in [Alpakka repository](https://github.com/akka/alpakka/blob/d0b0dde195407a0ec2c95447ac198f40d4bf502c/project/plugins.sbt#L14)</sup>

When `sbt-unidoc` generates one single site, it adds all of the source files and dependencies to one single classpath ands runs scaladoc tool on that.
For Alpakka project, this has caused some problems, where some dependencies across different Alpakka connectors are not compatible.
Such dependencies are then filtered out by configuring classpath of the `unidoc` task:

```scala
ScalaUnidoc / unidoc / fullClasspath := {
  (ScalaUnidoc / unidoc / fullClasspath).value
    .filterNot(_.data.getAbsolutePath.contains("protobuf-java-2.5.0.jar"))
    .filterNot(_.data.getAbsolutePath.contains("guava-27.1-android.jar"))
    .filterNot(_.data.getAbsolutePath.contains("commons-net-3.1.jar"))
}
``` 
<sup>Example in [Alpakka repository](https://github.com/akka/alpakka/blob/d0b0dde195407a0ec2c95447ac198f40d4bf502c/build.sbt#L73-L78)</sup>

### Reference documentation

The documentation that illustrates how API should be used is written with the [`paradox`](https://github.com/lightbend/paradox/) tool.
Paradox supports markdown syntax and also allows easy extension using sbt plugins.
The following sbt plugins are used to extend paradox functionality:

The Akka family theme is applied by the [`sbt-paradox-akka`](https://github.com/akka/akka-paradox) plugin.
Every Alpakka connector has a [list of its dependencies](https://doc.akka.io/docs/alpakka/current/amqp.html#artifacts) generated automatically by the [`sbt-paradox-dependencies`](https://github.com/lightbend/sbt-paradox-dependencies) plugin.
The [`sbt-paradox-project-info`](https://github.com/lightbend/sbt-paradox-project-info) plugin generates [project details table](https://doc.akka.io/docs/alpakka/current/s3.html#aws-s3) from a computer readable HOCON data source.

Reference documentation usually links to parts of the API documentation.
This is automated by the [`sbt-paradox-apidoc`](https://github.com/lightbend/sbt-paradox-apidoc) plugin.
The plugin allows to specify the shortest part of a fully qualified class name that distinguishes the class uniquely.
It then generates the appropriate API doc links for Scala and Java parts of the documentation.
An example of the plugin usage can be found in the [Akka reference documentation](https://github.com/akka/akka/blob/e2dc81862578b8b7f49cf7664918f1bb7998337c/akka-docs/src/main/paradox/stream/operators/RestartFlow/onFailuresWithBackoff.md#L3).

It is also possible to link to the outside API docs that describe classes of dependencies.
In that case URLs where the dependency API doc is hosted need to be provided as paradox properties.
Here is [an example of Alpakka providing links](https://github.com/akka/alpakka/blob/d0b0dde195407a0ec2c95447ac198f40d4bf502c/build.sbt#L306-L333) to various dependency reference and API documentation.

### Bringing it all together

The [`sbt-site`](https://github.com/sbt/sbt-site) plugin is used to put API and reference documentation together under one single interlinked site.
For local development `sbt-site` provides the `previewSite` task, which generates all of the documentation, starts an internal web server, and opens a tab in the browser pointing to the just generated website.  

## Release automation

The intention of the automated release is as little human operator action needed as possible.
Akka family projects use release issue templates to document all of the steps which are needed to make a release.
Here is an example of [Alpakka](https://github.com/akka/alpakka/blob/64d7da956590a06e68c627a7bbbe6482945c649f/docs/release-train-issue-template.md) project release issue template.

A couple of sbt plugins are also used to increase the automation of the release.

For example, artifact versions are automatically derived from git history by the [`sbt-dynver`](https://github.com/dwijnand/sbt-dynver) plugin.
This allows to have unique versions for snapshots which are published by Travis on every successful master build.
Also, when a tagged commit is being build, the plugin pulls in the version of the release.
`sbt-dynver` is also used to derive previous stable version, which is then used to discover artifacts to check binary compatibility against.

Binary compatibility is verified with [MiMa](https://github.com/lightbend/mima) which compares class signatures of two versions to discover non-binary-compatible changes.

```scala
mimaPreviousArtifacts := Set(
  organization.value %% name.value % previousStableVersion.value
   .getOrElse(throw new Error("Unable to determine previous version"))
)
```
<sub>Example in [Alpakka repository](https://github.com/akka/alpakka/blob/64d7da956590a06e68c627a7bbbe6482945c649f/build.sbt#L376-L379)</sub>

During the release build on Travis, the generated documentation is copied to https://doc.akka.io with the tiny [`sbt-publish-rsync`](https://github.com/lightbend/sbt-publish-rsync) plugin that invokes `rsync` on the host system.

<sub>Example in [Alpakka repository](https://github.com/akka/alpakka/blob/e5303b8143703f2eee131b1627c130eaffb019ec/build.sbt#L337-L338)</sub>

## Conclusion 

sbt build moduliarity allows to prototype solutions to infrastructure problems in the project build itself.
If the idea proves itself, then the code can be moved to the independent sbt plugin, which then can be shared across other projects.
This was the way how many of the sbt plugins described above came about.

We hope that you will find them useful as well.

Happy hakking,
The Alpakka Team
