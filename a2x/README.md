#Asciidoc / A2X Plugin for Gradle

## Motivation

Q: Why write a gradle plugin for ```asciidoc``` when the brilliant ```asciidoctor``` plugin already exists?

A:  Certain operations are not yet possible with ```asciidoctor``` and I wanted a way to

+ compare outputs between ```asciidoc``` and ```asciidoctor```
+ create an easy transition between moving the two as more functionality becomes available in ```asciidoctor```.

My recommendation is to use the ```asciidoctor``` plugin for most use cases

## Prerequisites

This plugin requires that ```asciidoc``` is installed on your system. If ```asciidoc``` and ```a2x``` is not on the execution path it can
be set via

```groovy
a2x {
  asciidochome '/path/to/asciidoc'
}
```

## Usage


```groovy
buildscript {
    repositories {
        jcenter()
        // http://oss.jfrog.org
    }

    dependencies {
        classpath 'org.ysb33r.gradle:a2x-gradle-plugin:0.1-SNAPSHOT'
    }
}

apply plugin: 'a2x'

```Asciidoctor Gradle Plugin
=========================
:version: 0.8.0.SNAPSHOT
:asciidoc-url: http://asciidoc.org
:asciidoctor-url: http://asciidoctor.org
:issues: https://github.com/asciidoctor/asciidoctor-maven-plugin/issues
:gradle-url: http://gradle.org/
:asciidoctor-maven-plugin: https://github.com/asciidoctor/asciidoctor-maven-plugin
:lightguard: https://github.com/LightGuard
:asciidoctor-java-integration: https://github.com/asciidoctor/asciidoctor-java-integration
:lordofthejars: https://github.com/lordofthejars
:asciidoctor-docs: http://asciidoctor.org/docs/

image:https://travis-ci.org/asciidoctor/asciidoctor-gradle-plugin.png?branch=master["Build Status", link="https://travis-ci.org/asciidoctor/asciidoctor-gradle-plugin"]
image:https://coveralls.io/repos/asciidoctor/asciidoctor-gradle-plugin/badge.png["Coverage Status", link="https://coveralls.io/r/asciidoctor/asciidoctor-gradle-plugin"]
image:http://img.shields.io/:semver-{version}-blue.svg["Semantic Versioning", link="http://semver.org"]

image::https://api.bintray.com/packages/aalmiray/asciidoctor/asciidoctor-gradle-plugin/images/download.png["Bintray", link="https://bintray.com/aalmiray/asciidoctor/asciidoctor-gradle-plugin"]

The asciidoctor-gradle-plugin is the official means of using {asciidoctor-url}[Asciidoctor] to render all your {asciidoc-url}[AsciiDoc] documentation using {gradle-url}[Gradle].

This is a port of the {asciidoctor-maven-plugin}[asciidoctor-maven-plugin] project by {lightguard}[@LightGuard]. Relies on {asciidoctor-java-integration}[asciidoctor-java-integration] by {lordofthejars}[@lordofthejars].

== Installation

Use the following snippet inside a Gradle build file:

.build.gradle
[source,groovy]
[subs="attributes"]
----
buildscript {
    repositories {
        jcenter()
    }

    dependencies {
        classpath 'org.asciidoctor:asciidoctor-gradle-plugin:{version}'
    }
}

apply plugin: 'asciidoctor'
----

== Usage

The plugin adds a new task named `a2x`. This task exposes 7 properties as part of its configuration

[horizontal]
sourceDir:: where the asciidoc sources are. Type: File. Default: `src/asciidoc`.
sourceDocumentName:: an override to process a single source file. Type: File. Defaults to all files in `${sourceDir}`
sourceDocumentNames:: an override to process multiple source files, which may be a subset of all
files available in `${sourceDir}`. This property has precedence over `sourceDocumentName` which means the
latter will be discarded if the former is defined. Type: FileCollection. Defaults to all files in `${sourceDir}`
outputDir:: where generated docs go. Type: File. Default: `$buildDir/asciidoc`.
backend:: the backend to use. Type: String. Default: `html5`. This option is a convenience for using backends property.
backends:: the backends to use. Type: Set<String>. Default: [`html5`].
options:: a Map specifying different options that can be sent to Asciidoctor.
logDocuments:: a boolean specifying if documents being processed should be logged on console. Type: boolean. Default: `false`
a2x:: Location of the a2x binary.

Sources may have any of the following extensions in order to be discovered

 * .asciidoc
 * .adoc
 * .asc
 * .ad

The following attributes are automatically set by the `Asciidoctor` task

 * project-name : matches $project.name
 * project-version: matches $project.version (if defined). Empty String value if undefined
 * project-group: matches $project.group (if defined). Empty String value if undefined

These attributes may be overridden by explicit user input.

== Configuration

The following options may be set using the task's `options` property

 * header_footer - boolean
 * template_dir - String
 * template_engine - String
 * compact - boolean
 * doctype - String
 * attributes - Map

Any key/values set on `attributes` are sent as is to Asciidoctor. You may use this Map to specify an stylesheet for example. The following snippet shows a sample configuration defining attributes

.build.gradle
[source,groovy]
----
// append below the line: apply plugin: 'asciidoctor'

asciidoctor {
    outputDir = new File("$buildDir/docs")
    options = [
        eruby: 'erubis',
        attributes: [
            'source-highlighter': 'coderay',
            toc: '',
            idprefix: '',
            idseparator: '-'
        ]
    ]
}
----

You may need to include extra content into the head of the exported document.
For example, you might want to include jQuery inside the `<head>` element of the HTML export.
To do so, first create a docinfo file `src/asciidoc/docinfo.html` containing the content to include, in this case the `<script>` tag to load jQuery.

.src/asciidoc/docinfo.html
[source,html]
----
<script src="//cdnjs.cloudflare.com/ajax/libs/jquery/2.0.3/jquery.js"></script>
----

Then, add the `docinfo1` attribute to the attributes list in the previous example:

.build.gradle
[source,groovy]
----
attributes: [
    // ...
    docinfo1: '',
    // ...
]
----

The value of `attributes` my be specified as a Map, List, Array or String, for example the following Map definition

.build.gradle
[source,groovy]
----
options = [
    attributes: [
        toc: 'right',
        'source-highlighter': 'coderay',
        'toc-title': 'Table of Contents'
    ]
]
----

may be rewritten in List/Array form as follows

.build.gradle
[source,groovy]
----
options = [
    attributes: [
        'toc=right',
        'source-highlighter=coderay',
        'toc-title=Table of Contents'
    ]
]
----

or in String form like so

.build.gradle
[source,groovy]
----
options = [
    attributes: 'toc=right source-highlighter=coderay toc-title=Table\\ of\\ Contents'
]
----

Notice how spaces are escaped in the last key/value pair.

Refer to the {asciidoctor-docs}[Asciidoctor documentation] to learn more about these options and attributes.

image::https://d2weczhvl823v0.cloudfront.net/asciidoctor/asciidoctor-gradle-plugin/trend.png["Bitdeli Badge", link="https://bitdeli.com/free"]
