# Changelog
All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.5.2] - 2022-09-28

* remove signing from library

## [1.5.1] - 2022-08-24

* added basic-library.gradle for reuse to avoid c/p


## [1.5.0] - 2022-08-24

* Fix issue with POM generation for mavenCentral()
* aar publication now require 1.5.0 version minimum

## [1.4.4] - 2022-07-07

- Added aar publishing javadoc/sourceSet
- Removed repositories.gradle
- Updated Readme

## [1.4.3] - 2022-07-04

- Added v1 and v2 schema to signingConfig

## [1.4.2] - 2022-06-20

- Bug fixes

## [1.4.1] - 2022-06-20

- Bug fixes

## [1.4.0] - 2022-06-20

- Added support for publishing to Maven Central
- Removed support for publishing to old AWS S3 repos
- Removed redundant jar/aar publisher files

## [1.3.0] - 2022-02-21

- Breaking changes to locations of publication scripts
- Updated to latest Gradle and fixed visibility problems