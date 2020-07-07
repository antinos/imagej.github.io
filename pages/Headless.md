---
autogenerated: true
title: Headless
breadcrumb: Headless
layout: page
author: test author
categories: 
description: test description
---

[ImageJ 1.x](ImageJ1 "wikilink") was never meant as anything else than a
desktop application with one user in front of one screen attached to one
computer.

However, it acquired [macro](macro "wikilink") capabilities, a [batch
mode](batch_mode "wikilink") for such macros, and even
[scripting](scripting "wikilink") support.

Naturally, users want to execute such [macros](macros "wikilink") or
[scripts](scripts "wikilink") in environments such as clusters where
there is no graphical user interface available.

# The problem

Java *does* support a headless mode via the `java.awt.headless`
property; setting this property to `true` enables it.

Unfortunately, with X11-based Java (such as on Linux, which is the most
prevalent platform for running clusters), headless mode does not allow
to instantiate any GUI components that would want to display text. The
reason is that the font-metrics on X11 are provided by the X11 server
(and are indeed different between servers) and therefore the dimensions
of such elements simply cannot be calculated without a graphical
desktop.

Since ImageJ 1.x was devised as a desktop application, everything --
including macros -- works through the GUI. For example, a simple
`run("Open...");` will look for the action in the menu.

On MacOSX, there is no problem: Aqua provides GUI-independent text
rendering (mapping to the actual display using anti-aliasing). There,
running in headless mode allows instantiating GUI elements such as the
menu bar.

# Possible solutions

## The `--headless` mode

[ImageJ2](ImageJ2 "wikilink") provides the capability to execute ImageJ
plugins, macros and scripts in headless mode. This feature uses bytecode
manipulation to patch ImageJ 1.x's behavior at runtime, making it
possible to start ImageJ in batch mode without instantiating GUI
components.

**Shortcoming:** There are plugins which are even more bound to a GUI
than ImageJ 1.x is. Naturally, these plugins will still try to
instantiate GUI elements when being called in headless mode, failing.

### Running scripts in headless mode

Please see the [headless scripting
guide](Scripting_Headless "wikilink").

### Running macros in headless mode

To run a *macro* in headless mode, use the `-macro` command line
argument along with the `--headless` option, as follows:

` ImageJ --headless -macro path-to-Macro.ijm`

If the macro resides in ImageJ's macro directory, it is possible to
specify the macro name instead of the actual file path. The file
extension is always very recommended but for backwards compatibility, it
is not strictly required *only* when specifying the macro name instead
of a path.

You can even pass parameters to the macro; e.g.:

` ./ImageJ-win64.exe --headless --console -macro ./RunBatch.ijm 'folder=../folder1 parameters=a.properties output=../samples/Output'`

In that case, the RunBatch.ijm file should be something like:

    arg = getArgument()
    print("Running batch analysis with arguments:")
    print(arg)
    run("Batch process", arg )
    print("Done.")
    eval("script", "System.exit(0);");

the `getArgument()` is used to grab the parameter string itself, and it
is then passed to an IJ command.

## Xvfb

Another method is to have a virtual desktop, e.g.
[Xvfb](wikipedia:Xvfb "wikilink"). This will allow ImageJ to start with
a virtualised graphical desktop.

**Advantage:** No run-time patching is required.

**Shortcomings:** It is slower than it needs to be because of the
overhead of starting the GUI, it is harder to configure, and plugins
might get stuck because they wait for user input which never comes.

### Examples

Here are a couple of simple examples.

Passing direct arguments:

``` bash
$ cat hello.js
importClass(Packages.ij.IJ);
IJ.log("hello " + arguments[0]);
$ xvfb-run -a $IMAGEJ_DIR/ImageJ-linux64 hello.js Emerson
hello Emerson
```

With [SciJava script parameters](Script_Parameters "wikilink"):

``` bash
$ cat hello-with-params.js
// @String name
importClass(Packages.ij.IJ);
IJ.log("hello " + name);
$ xvfb-run -a $IMAGEJ_DIR/ImageJ-linux64 --ij2 --headless --run hello-with-params.js 'name="Emerson"'
hello Emerson
```

A more complex shell script that wraps a macro for use with Xvfb (thanks
to Nestor Milyaev):

``` bash
export DISPLAY=:1
Xvfb $DISPLAY -auth /dev/null &
(
# the '(' starts a new sub shell. In this sub shell we start the worker processes:

script=$scriptDir"lsmrotate2nrrd.ijm \"dir="$1"&angle-x=$2&angle-y=
$3&angle-z=$4&reverse=$5\" -batch"
$imagejBin -macro $script # running the actual ijm script

wait # waits until all 'program' processes are finished
# this wait sees only the 'program' processes, not the Xvfb process
)
```

See also [this post on the ImageJ mailing
list](https://list.nih.gov/cgi-bin/wa.exe?A2=IMAGEJ;5ace1ed0.1508).

## Rewriting as scripts or plugins

The most robust method is to rewrite macros as scripts that do not
require interaction with the GUI to begin with. Unfortunately, this is
the most involved solution, too, since it usually takes some time to
convert macros.
[ImageJ 1.x](ImageJ1 "wikilink") was never meant as anything else than a
desktop application with one user in front of one screen attached to one
computer.

However, it acquired [macro](macro "wikilink") capabilities, a [batch
mode](batch_mode "wikilink") for such macros, and even
[scripting](scripting "wikilink") support.

Naturally, users want to execute such [macros](macros "wikilink") or
[scripts](scripts "wikilink") in environments such as clusters where
there is no graphical user interface available.

# The problem

Java *does* support a headless mode via the `java.awt.headless`
property; setting this property to `true` enables it.

Unfortunately, with X11-based Java (such as on Linux, which is the most
prevalent platform for running clusters), headless mode does not allow
to instantiate any GUI components that would want to display text. The
reason is that the font-metrics on X11 are provided by the X11 server
(and are indeed different between servers) and therefore the dimensions
of such elements simply cannot be calculated without a graphical
desktop.

Since ImageJ 1.x was devised as a desktop application, everything --
including macros -- works through the GUI. For example, a simple
`run("Open...");` will look for the action in the menu.

On MacOSX, there is no problem: Aqua provides GUI-independent text
rendering (mapping to the actual display using anti-aliasing). There,
running in headless mode allows instantiating GUI elements such as the
menu bar.

# Possible solutions

## The `--headless` mode

[ImageJ2](ImageJ2 "wikilink") provides the capability to execute ImageJ
plugins, macros and scripts in headless mode. This feature uses bytecode
manipulation to patch ImageJ 1.x's behavior at runtime, making it
possible to start ImageJ in batch mode without instantiating GUI
components.

**Shortcoming:** There are plugins which are even more bound to a GUI
than ImageJ 1.x is. Naturally, these plugins will still try to
instantiate GUI elements when being called in headless mode, failing.

### Running scripts in headless mode

Please see the [headless scripting
guide](Scripting_Headless "wikilink").

### Running macros in headless mode

To run a *macro* in headless mode, use the `-macro` command line
argument along with the `--headless` option, as follows:

` ImageJ --headless -macro path-to-Macro.ijm`

If the macro resides in ImageJ's macro directory, it is possible to
specify the macro name instead of the actual file path. The file
extension is always very recommended but for backwards compatibility, it
is not strictly required *only* when specifying the macro name instead
of a path.

You can even pass parameters to the macro; e.g.:

` ./ImageJ-win64.exe --headless --console -macro ./RunBatch.ijm 'folder=../folder1 parameters=a.properties output=../samples/Output'`

In that case, the RunBatch.ijm file should be something like:

    arg = getArgument()
    print("Running batch analysis with arguments:")
    print(arg)
    run("Batch process", arg )
    print("Done.")
    eval("script", "System.exit(0);");

the `getArgument()` is used to grab the parameter string itself, and it
is then passed to an IJ command.

## Xvfb

Another method is to have a virtual desktop, e.g.
[Xvfb](wikipedia:Xvfb "wikilink"). This will allow ImageJ to start with
a virtualised graphical desktop.

**Advantage:** No run-time patching is required.

**Shortcomings:** It is slower than it needs to be because of the
overhead of starting the GUI, it is harder to configure, and plugins
might get stuck because they wait for user input which never comes.

### Examples

Here are a couple of simple examples.

Passing direct arguments:

``` bash
$ cat hello.js
importClass(Packages.ij.IJ);
IJ.log("hello " + arguments[0]);
$ xvfb-run -a $IMAGEJ_DIR/ImageJ-linux64 hello.js Emerson
hello Emerson
```

With [SciJava script parameters](Script_Parameters "wikilink"):

``` bash
$ cat hello-with-params.js
// @String name
importClass(Packages.ij.IJ);
IJ.log("hello " + name);
$ xvfb-run -a $IMAGEJ_DIR/ImageJ-linux64 --ij2 --headless --run hello-with-params.js 'name="Emerson"'
hello Emerson
```

A more complex shell script that wraps a macro for use with Xvfb (thanks
to Nestor Milyaev):

``` bash
export DISPLAY=:1
Xvfb $DISPLAY -auth /dev/null &
(
# the '(' starts a new sub shell. In this sub shell we start the worker processes:

script=$scriptDir"lsmrotate2nrrd.ijm \"dir="$1"&angle-x=$2&angle-y=
$3&angle-z=$4&reverse=$5\" -batch"
$imagejBin -macro $script # running the actual ijm script

wait # waits until all 'program' processes are finished
# this wait sees only the 'program' processes, not the Xvfb process
)
```

See also [this post on the ImageJ mailing
list](https://list.nih.gov/cgi-bin/wa.exe?A2=IMAGEJ;5ace1ed0.1508).

## Rewriting as scripts or plugins

The most robust method is to rewrite macros as scripts that do not
require interaction with the GUI to begin with. Unfortunately, this is
the most involved solution, too, since it usually takes some time to
convert macros.