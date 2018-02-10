# Juris-M build scripts

A repository of scripts, nearly identical to its Zotero fork-parent, used to prep the Juris-M code module for integration into the standalone client.

(Before working with these scripts, be sure to visit the core Juris-M repository and perform the steps for building the core module that are described there.)

----------

## Notes from the Zotero repository

2xize: Add 2x image rules to CSS files

update-citeproc.sh: Update citeproc.js from Frank Bennett's Bitbucket source

xpi: Build distributable Firefox XPIs from the [Zotero core](https://github.com/zotero/zotero).

----------

## About this repository

Zotero was orginally developed as a plugin that could be installed in a standard release of the Firefox browser. Due to architectural changes in Firefox, it can no longer be installed in that fashion, and in Juris-M development, at least, the scripts in this repository designed for building and signing the plugin (an ``*.xpi`` file) are no longer used.

However, one script, ``./xpi/build_xpi``, serves several important roles
in building the standalone client.

* It resolves soft links set in the ``../jurism/build`` directory when the core ``jurism`` module is initially assembled there.
* It manages the version stamp used to identify specific builds of the module code.

The steps for configuring and using the script are outlined here.

## Cloning the repository

Enter the directory where your Juris-M development repositories are located, and clone this into it, using commands like the following:
```bash
  prompt> cd jurism-repos
  prompt> git clone --recursive https://github.com/Juris-M/zotero-build.git
```
If you forget to include the ``--recursive`` option when cloning, pull in the submodules by entering the repository directory and issuing commands like the following:
```bash
  prompt> cd zotero-build
  prompt> git submodule init
  prompt> git submodule update --remote
```

## Configuration

For Juris-M work, at least at present, no configuration is necessary. The ``config.sh-sample`` files would be used as templates to set environment variables used by the ``update-citeproc.sh`` script (in the top-level directory) and the ``move_build_rdf`` and ``push_dev_xpi`` scripts (in the ``./xpi`` directory). These are not currently used in Juris-M work (by me, anyway).

The behavior of the ``build_xpi`` script is controlled by command-line options.

## Building

While this repository of scripts manipulates only the core Juris-M module code, the version stamp that it applies to the source becomes the version shown in the built client. Builds, and versions, come in three flavors: source, beta, and release. The steps for generating each are described below.

### Source builds

Source builds are mainly meant for private use when debugging or extending the module. They are identified by a hash appended to the version string, which (if things were done properly) matches the commit in the core Juris-M code repository against which it has been built. For example:
```
  5.0.34.m1.SOURCE.552662a42
```
Generate a source build by providing two options to ``build_xpi``:
```bash
  prompt> ./xpi/build_xpi --source-dir ../jurism/build --commit-hash 552662a42556e7b9c3ad690a9be3a6365730205f 
```
Reviewing the options:
> ``--source-dir`` is the path to the built source in the core Juris-M code repository. The ``build_xpi`` script does not generate this source, so the ``build`` directory must exist, and its content must be up to date.

> ``--commit-hash`` is the commit ID in the core Juris-M code repository that is reflected in the ``--source-dir`` build directory. The ``build_xpi`` script does not check out this commit in the repository, the value supplied in the argument is informational only.

### Beta builds

Beta builds are used to share code before a final release. This designation is not much used Juris-M development, although each Juris-M release is in effect a beta, since intensive pre-release testing is not practical, given our modest user numbers. When explicitly declared, a beta is identified as such in its version string, with a serial number. For example:
```
  5.0.34.m1-beta.1+552662a42
```
Generate a beta build by providing three options to ``build_xpi``:
```bash
  prompt> ./xpi/build_xpi --source-dir ../jurism/build --channel beta --commit-hash 552662a42556e7b9c3ad690a9be3a6365730205f 
```
Reviewing the options:
> ``--channel`` is the name of a numbered stream of pre-release builds. By convention these carry the label ``beta``, but the label can be anything. The number of a build within a labeled series is incremented by 1 each by a new build is performed. The numbering is controlled by files add under ``./xpi/build`` each time the ``build_xpi`` script is run with the ``--channel`` option.

> ``--source-dir`` is the path to the built source in the core Juris-M code repository. The ``build_xpi`` script does not generate this source, so the ``build`` directory must exist, and its content must be up to date.

> ``--commit-hash`` is the commit ID in the core Juris-M code repository that is reflected in the ``--source-dir`` build directory. The ``build_xpi`` script does not check out this commit in the repository, the value supplied in the argument is informational only.



### Release builds

A release build is the last to carry its version number, which must be incremented for the next beta or final release. Juris-M hedges its bets by appending a suffix to the Zotero release on which it is based, so that further "final" releases can be made in the event issues arise that are specific to Juris-M. The version number of a final release is written without trailing cruft. For example:
```
  5.0.34.m1
```
Generate a release build by providing two options to ``build_xpi``:
```bash
  prompt> ./xpi/build_xpi --source-dir ../jurism/build --channel release
```
Reviewing the options:
> ``--channel`` must be set to ``release`` in this instance. Other values will throw an error.

> ``--source-dir`` is the path to the built source in the core Juris-M code repository. The ``build_xpi`` script does not generate this source, so the ``build`` directory must exist, and its content must be up to date.

## Building the standalone client

Running ``build_xpi`` in any of the three modes documented above will generate a stream of chatter in the console, ending with a line like this:
```bash
  Build files saved to /path/to/zotero-build/xpi/build/staging
```
The ``staging`` directory does not actually contain an XPI file, but it does contain a complete set of code that can be run in an appropriate Firefox environment. Make a note of the path value; you will need it to build the actual standalone client. See the README in the ``zotero-standalone-build`` repository for details on that process.
