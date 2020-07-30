{{Notice | The following article was written by {{Person|Scrivello}} as he researched how to write plugins for [[ImageJ2]]. It is reproduced here as a [[News]] post in the hope that it may help others, particularly during 2016 while the information continues to be most accurate and relevant.}}

{{TOC}}
= Writing ImageJ2 Plugins: A Beginner's Perspective =

== Preamble ==

Before you consider to write your own [[plugin]] for [[ImageJ]], please note that writing a [[script]] has a much lower barrier to entry than Java plugin development.

Also you may want to have a look at [[Introduction_into_Macro_Programming]] as an easy way to automate repetitive tasks with existing tools and plugins.

== Finding information ==

* The top-level source of information is [[Welcome|the ImageJ wiki]].
* The [[Development]] page is the best portal for aspiring ImageJ developers.
* [[ImageJ2]] is available at: {{GitHub | org=imagej | repo=imagej | label=imagej/imagej}}
* [[Fiji]] is available at: {{GitHub | org=fiji | repo=fiji | label=fiji/fiji}}
* For additional help, use the [[ImageJ Forum]].

The search engines will point you to both [https://imagej.net/ https://imagej.net/] and [http://developer.imagej.net/ http://developer.imagej.net/]. Avoid using [http://developer.imagej.net/ http://developer.imagej.net/] for anything these days. It is a legacy site, in the process of being totally phased out. If you are looking for downloads, see the [[Downloads]] page.

== Configure your environment ==

You will need:

* Java
* [[IDE]] (for example: [[Eclipse]], [[NetBeans]] or [[IntelliJ_IDEA]])  is highly recommended
* [[Git]]
* [[Maven|Apache Maven]]
* [[Source code|ImageJ2 source code]] is an optional help

Environments used for testing this guide:

* Debian Jessie | macOS 10.14.x
* OpenJDK 1.8.x
* NetBeans IDE version 8.0.2 | IntelliJ IDEA 2019.2.2 (Community Edition)
* Git 2.x
* Apache Maven 3.x

[[Git]] is a source-code-management system with revision control.

[[Maven]] is a build automation tool used primarily for Java projects.

Both Git and Maven have build in support in current versions of the IDEs mentioned above.

Earlier versions of the ImageJ wiki [[Project management|mentioned another software tool]], called [[Jenkins]], which is according to Wikipedia "an open source continuous integration tool written in Java". This tool was later replaced by a similar working service called [[Travis CI]]. Tools like Maven and Travis CI make it more complicated for casual developers to understand the workflow of ImageJ Plugin development at the beginning. However, these tools are well maintained by the community so that you will not have to think about them too much initially and they facilitate the programming process.    
For example, concerning "Travis CI" it is enough to know that "Continuous Integration" means merging all developer working copies to a shared mainline several times a day. Here the work of multiple developers is compiled and tested on a single machine to ensure that the combined code produces a working project at any time. For the development of a single plugin for our personal use we can ignore "Travis CI" for the moment. It will be important if you want to share your plugin in the ImageJ updater or even contribute to the ImageJ project.

All [[source code]] is on [[GitHub]]. As ImageJ nowadays is a rather complex project its development is split into several [[Architecture|ImageJ subprojects]]. For a beginner it is hard to understand the interaction of the different available projects which all contribute under the label "[[SciJava]]" to ImageJ2. The nice thing is, that Maven will help to pull in the necessary code from all ImageJ subprojects automatically with the help of configurations files which are supplied by the ImageJ2 developer community. The ImageJ wiki provides a very first overview of the [[Architecture|SciJava ecosystem]] of ImageJ2.

To make Maven work we need so called <code>pom.xml</code> files. These configuration file contains information about the project and various configuration details used by Maven to build the project(s).   
The <code>pom.xml</code> files help to organize everything needed to build ImageJ. You can use any Maven-based project you want with that approach, not just ImageJ. So e.g. you can import {{GitHub|org=fiji|repo=fiji|label=fiji/fiji}} that way, or an individual plugin such as {{GitHub|org=fiji|repo=AnalyzeSkeleton|label=fiji/AnalyzeSkeleton}}.

In general, there are two alternative strategies to develop your plugin:

* Use your IDE without Maven
* Use your IDE with Maven

== Using NetBeans without Maven ==

When you build your own plugins with an IDE, the ImageJ project will link in all plugins as precompiled JAR dependencies (JAR files are archives for java projects).

Download the current JAR files, e.g. [http://jenkins.imagej.net/job/ImageJ/lastSuccessfulBuild/artifact/target/ imagej-2.0.0-SNAPSHOT-all.jar], which you intend to use as library in your local project.

You could also use the JAR file to compile your own plugins, which are distributed in the <code>Fiji.app/plugins</code> directory.

If you download Fiji via [[Fiji/Downloads]] then take care to select the right version of Fiji. The most prominent download option on top of the page is compiled with JDK 1.8, while you can download so-called "life-line" versions at the bottom of the page which are compiled with JDK 1.6 to ensure compatibility with older plugins not supported by the ImageJ2 Team. For details [[2015-12-22 - The road to Java 8|look here]].

In any case you need to open a new project, assign the project name, its directory location and add the JAR files as libraries.

* {{bc|File|New Project}}: Java Application (Project Name, Project Location)
* Right click on the Project in the tree view {{bc|window|Properties}}
* {{bc|Libraries|Add JAR/Folder}}: point to the JAR files you downloaded for ImageJ/Fiji.

A somewhat outdated but rather detailed description how to work with an older version of NetBeans (version 6.7) including bit of customization of the <code>build.xml</code> file can be found [http://www.dent.med.uni-muenchen.de/~kkunzelm/htdocs/6_software-netbeans.html here].

{{Warning | Please consider:

Using JAR works for local development, but it will cause you various problems later. For example, if you share your source code, you will need to commit this JAR along with your NetBeans project files to your repository, to ensure everyone else uses the same version of ImageJ that you do.}}

== Build ImageJ with NetBeans, Git and Maven ==

Using Maven to develop your plugins is a much better approach. You will not have to commit any JAR files to source control. You can pin your code to fixed, known versions of its dependencies that will provide [[reproducible builds]] for many years to come.

Getting the ImageJ sources in NetBeans should be as simple as importing the source from the Git repository.

The following was adapted from the [[Developing ImageJ in NetBeans]] page.

Import and build the project:
* Run NetBeans
* Choose {{bc|Team|Git|Clone...}} from the NetBeans menu
* For the Repository URL, enter: <code>https://github.com/fiji/fiji</code> or alternatively enter <code>https://github.com/imagej/imagej</code>
* Click Next, check the <code>master*</code> branch, then Next again, then Finish
* When prompted, click Open Project... in case of Error Messages click on "Resolve".

Launch the program:
* Expand the "ImageJ Projects" project, then "Modules"
* Expand the "ImageJ POM: User Interface" module
* Double-click the "ImageJ Application" project to open it
* Right-click the "ImageJ Application" project and choose "Run"
* On the Main Class dialog, choose "net.imagej.Main"
* To expand the projects you can also right click on the top-level "ImageJ Projects" and choose "Open Required Projects" (and "Close Required Projects" to close). During development you must select "Open Required Projects" before you can successfully do "Find Usages" in the "Open Projects" scope.

Do not expect to find the ImageJ sources after cloning {{GitHub|org=imagej|repo=imagej|label=imagej/imagej}} or {{GitHub|org=fiji|repo=fiji|label=fiji/fiji}}.

If you want to look at the source code to study how to program image analysis algorithms then you will need to clone other GitHub projects.

The repository {{GitHub | org=imagej | repo=imagej1 | label=imagej/imagej1}}, for example, contains the source code of [[ImageJ 1.x]], but it does not use Maven. The [[ImageJA]] project at {{GitHub | org=imagej | repo=ImageJA | label=imagej/ImageJA}} is a Mavenized version of ImageJ 1.x with a clean Git history. For curious people like me: the "A" in ImageJA was originally used for "Applet" and to differentiate the project from ImageJ itself.

{{Notice |
Side note: I am not expert NetBeans user, therefore I could not figure out another way:

To import several projects from GitHub I always had to close all open project in NetBeans before I could import another project. If somebody knows a better way, please add here.}}

== Build the ImageJ Tutorial Plugin with IntelliJ IDEA, Git and Maven ==

'''Setup IntelliJ'''
* Make sure you have a version of Java SDK 1.8.x installed
* Make sure to activate Maven and Git Plugins when installing IntelliJ
* However these plugins can be activated in the main settings at any time

'''Import and build the project:'''
* Run IntelliJ IDEA
* Choose {{bc|File|New|Project from Version Control|Git}} from the main menu
''ImageJ 1.x Plugin'' 
* For the Repository URL, enter: <code>https://github.com/imagej/example-legacy-plugin.git</code> and choose a local folder you want the project to be stored in
''ImageJ2 Plugin''   
* For the Repository URL, enter: <code>https://github.com/imagej/tutorials.git</code> and choose a local folder you want the project to be stored in
'''Import'''
* Click Clone and all sources will be downloaded and opened as a new Project
* If IntelliJ asks to import all Maven changes you have to allow this
* Expand the Maven window which can be found on one of the edges in the IntelliJ window and select your project 
* Here you can right-click and "Run Maven Build" or alternatively press the green arrow above it in the Maven window to build your project
* The build process will generate two <code>.jar</code> file under ''[project_name]/targets/'' that can be installed in your local ImageJ installation

= Details of Writing Plugins =

== Tutorials ==

* For ImageJ 1.x: [[Introduction into Developing Plugins]] and [https://github.com/imagej/example-legacy-plugin Example Legacy Plugin]
* For ImageJ2: [[Writing plugins]] (Note: The instruction "Update your parent POM" in [[Writing plugins#Update_your_POM]] just means that the version number should be adjusted to reflect the latest available version of the parent POM file on GitHub.)
* [[ImgLib2 Examples]]
* [[Developing ImgLib2]]
* [[ImageJ Ops]]

== Getting Started ==

Start from an existing plugin as a template:

* For ImageJ 1.x plugins: {{GitHub | org=imagej | repo=minimal-ij1-plugin | label=imagej/minimal-ij1-plugin}}
* For ImageJ2 plugins: {{GitHub | org=imagej | repo=tutorials | path=maven-projects/simple-commands | label=simple-commands in imagej/tutorials}}

Import it as a sample project into your IDE and modify this project according to your needs:

* NetBeans: {{bc|File|Open Project}}

The following lines are copied/cited from the <code>README.md</code> file of the <code>minimal-ij1-plugin</code>:

* Edit the <code>pom.xml</code> file and change
** the <code>artifactId</code>
*** Note: for ImageJ 1.x plugins the <code>artifactId</code> should contain a <code>_</code> character. If you write an ImageJ2 command (like the ones linked above in <code>simple-commands</code> tutorial) then the underscore is unnecessary.
** the <code>groupId</code>
*** You should put a <code>groupId</code>. It is misleading to leave it off, because then <code>net.imagej</code> (or <code>sc.fiji</code> if you used <code>pom-fiji</code> as parent) will be inherited. And your project is probably not a core ImageJ project.
** the <code>version</code> (note that you typically want to use a version number ending in <code>-SNAPSHOT</code> to mark it as a work in progress rather than a final version)
** the <code>dependencies</code> (read how to specify the correct <code>groupId</code>/<code>artifactId</code>/<code>version</code> triplet here)
** the <code>developer</code> information
** the <code>scm</code> information
* Remove the <code>Process_Pixels.java</code> file and add your own <code>.java</code> files to <code>src/main/java/<package>/</code> (if you need supporting files&mdash;like icons&mdash;in the resulting <code>.jar</code> file, put them into <code>src/main/resources/</code>)
* Edit <code>src/main/resources/plugins.config</code>
** This is only needed for ImageJ 1.x plugins. For ImageJ2 commands, the information is provided by the <code>@Plugin</code> annotation at the top of the Java class.
* Replace the contents of <code>README.md</code> with information about your project.

== Additional sample plugins ==

{{GitHub | org=imagej | repo=tutorials | label=imagej/tutorials}}

The imagej/tutorials are structured as individual projects. The files can live in a directory on its own outside the ImageJ or Fiji project. The <code>pom.xml</code> files of the <code>imagej/tutorials</code> pull in all the necessary dependencies for compiling via Maven.

== "One file to bind them all": parent <code>pom.xml</code> files ==

As the projects get more complex, read about the [[Architecture#Maven_component_structure|Maven component structure of ImageJ/SciJava]] and something which is called "[[Bill of Materials]]" or just BOM. A "BOM" is a list of dependencies at particular versions which are believed to be mutually compatible. The complexity of ImageJ/SciJava's dependencies is a tribute to the different organizations which are contributing with their independent projects to ImageJ/SciJava. There are several "parent" pom.xml files which are independently maintained for example by the ImageJ, [[ImgLib2]] or [[SCIFIO]] organizations. Each of these organizations has developed source code components which depend on components within the other two organizations. This complicated network of dependencies is managed with the help of the parent <code>pom.xml</code> files, i.e. <code>pom-imagej</code>, <code>pom-fiji</code>, <code>pom-imglib2</code> etc. (see a list of all on the [[Architecture#Maven_component_structure|ImageJ Architecture page]]).

Initially I could not figure out where to put one of these <code>pom-xxx</code> files to use it as parent POM. I erroneously thought it should be downloaded from GitHub and copied somewhere in my ImageJ projects folders. However, one does not have to take care of the parent POM file at all! You just have to refer to it in the local <code>pom.xml</code> file of your intended plugin project in the section <code><parent></code>.

<source lang="xml">
<parent>
  <groupId>net.imagej</groupId>
  <artifactId>pom-imagej</artifactId>
  <version>15.1.0</version>
  <relativePath />
</parent>
</source>

If <code>pom-imagej</code> is the parent POM file, then the local <code>pom.xml</code> could override the following configuration sections:

<source lang="xml">
<name>
<description>
<url>
<inceptionYear>
<organization>
<licenses>
<developers>
<contributors>
<scm>
<issueManagement>
<ciManagement>
</source>
In the local <code>pom.xml</code> at least the sections:

<source lang="xml">
<groupId>
<artifactId>
<version>
</source>
should be changed. Optionally also:

<source lang="xml">
<name>
<description>
<url>
</source>

Finally add at least one of the following dependencies for ImageJ plugin support:   

<source lang="xml">
<dependencies>
	// support for ImageJ2 plugins
	<dependency>
	    <groupId>net.imagej</groupId>
	    <artifactId>imagej</artifactId>
	</dependency>
	// support for ImageJ 1.x plugins
	<dependency>
	    <groupId>net.imagej</groupId>
	    <artifactId>ij</artifactId>
	</dependency>
</dependencies>
</source>

== Further readings ==

* [https://github.com/imagej/minimal-ij1-plugin/blob/master/README.md README.md] of the [https://github.com/imagej/minimal-ij1-plugin Minimal Maven based ImageJ 1.x plugin]
* Learn more about [[Maven#How_to_find_a_dependency.27s_groupId.2FartifactId.2Fversion_.28GAV.29.3F|ImageJ/SciJava dependencies]]
* [[Maven - Frequently Asked Questions|ImageJ Maven FAQ]]

= Other References =

* [http://forum.imagej.net/t/java3d-issue-bonej-with-latest-fiji-version-problem-solved/1151 ImageJ Forum Thread 1151]
* [http://forum.imagej.net/t/guide-to-make-a-plugin-as-official-fiji-plugin/1290 ImageJ Forum Thread 1290]
* [http://forum.imagej.net/t/ij1-or-ij2-style-for-plugin-development/1364 ImageJ Forum Thread 1364]

* Git tutorial: [https://try.github.io/levels/1/challenges/1 Git in 15 min]
* Web interface for git: [[GitHub]]

and all other links cited in the text!

= Appendix =

== How do I find dependencies? ==

You can search by class for Maven artifacts. For example, [http://maven.imagej.net/#nexus-search;classname~ij.plugin.PlugIn search for <code>ij.plugin.PlugIn</code>]. There is also a "Find Jar For Class" helper script in Fiji which does a similar thing for JAR files currently on ImageJ's classpath.

If you are comfortable with command-line tools, you can also use the [https://maven.apache.org/plugins/maven-dependency-plugin/ Maven Dependency Plugin] which enables you to do things like download local copies of the dependency jars for inspection.

Also [https://mvnrepository.com mvnrepository.com] is a good resource to find repositories with code you can easily copy and paste in your <code>pom.xml</code>.

== Manage Java versions ==

On Linux several java version can be installed. Select the preferred version in a terminal window (e.g. bash):

<source lang="bash">
update-alternatives --config java
</source>
Note: It might be necessary to use <code>sudo</code>.

If necessary, tell NetBeans to use JDK 1.8 as the default JRE for new projects (i. e. on Debian Linux: {{bc|Project Properties|Build|Compile...}} <code>/usr/lib/jvm/java-1.8.0-openjdk-amd64</code>) or alternatively set the <code>netbeans_jdkhome</code> property in your NetBeans config file. It should be in the local NetBeans directory, for example <code>./netbeans-8.0/netbeans.conf</code>.

== Where can I find example plugins? ==

* [https://github.com/imagej/tutorials/ ImageJ Tutorials]
** In NetBeans: {{bc|Team|Git|Clone}}
** Repository URL: https://github.com/imagej/tutorials
** Edit "destination" directory
** Next
** Select <code>master*</code>
** Next
** Finish

== What is the directory structure for a plugin? ==

This text was adapted from the [[Maven]] page.

The directory structure of a very simple demo project looks like:

<pre>
DemoPlugin
|-- pom.xml
|-- src
|   !-- main
|       |-- java
|       |   !-- MyPlugin.java
|       !-- resources
|           !-- lena.tif
</pre>

After compiling your java files, Maven automatically generates the content of the <code>target</code> folder. Therefore: never commit any files from <code>target</code> to Git! You can tell Git to ignore these files by using a [https://help.github.com/articles/ignoring-files/ .gitignore] file (usually you start by copying [https://github.com/imagej/imagej/blob/95722503b4d2243b2818f8a7b5c2cdf863c5da69/.gitignore an existing one] from another project) 

<pre>
!-- target
    |-- classes
    |   |-- lena.tif
    |   |-- META-INF
    |   |   !-- json
    |   |       !-- org.scijava.plugin.Plugin
    |   !-- MyPlugin.class
    |-- generated-sources
    |   !-- annotations
    |-- maven-status
    |   !-- maven-compiler-plugin
    |       !-- compile
    |           !-- default-compile
    |               |-- createdFiles.lst
    |               !-- inputFiles.lst
    !-- test-classes
</pre>

In general:

Put your <code>.java</code> files under <code>src/main/java/</code> and the other files you need to be included into <code>src/main/resources/</code>.

Should you want to apply the best practices called "regression tests" or even "test-driven development" put your tests' <code>.java</code> files to <code>src/test/java/</code> and the non-<code>.java</code> files you might require go into <code>src/test/resources/</code>.

More information about Maven's standard directory layout can be found on the [https://maven.apache.org/guides/introduction/introduction-to-the-standard-directory-layout.html Maven website].

== What does a minimal <code>pom.xml</code> look like? ==

This text was adapted from the [[Maven]] page.

This is a very simple example:

<source lang="xml">
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
  http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>org.mywebsite</groupId>
  <artifactId>my-uber-library</artifactId>
  <version>2.0.0-SNAPSHOT</version>
</project>
</source>

The first 6 lines are of course just a way to say "Hi, Maven? How are you today? This is what I would like you to do...".

The only relevant parts are the <code>groupId</code>, which by convention is something like the inverted domain name (similar to the Java package convention), the name of the artifact to build (it will be put into <code>target/</code>, under the name <code><artifactId>-<version>.jar</code>). And of course the version.

While the example <code>pom.xml</code> above shows the general idea, for ImageJ more details have to be considered. Therefore it is better to start with an existing <code>pom.xml</code> file, for example the one from {{GitHub | org=imagej | repo=minimal-ij1-plugin | label=imagej/minimal-ij1-plugin}}. Copy it to your project and modify it as needed.

== Are the underscores still needed for plugins to be visible in IJ or Fiji? ==

From [http://forum.imagej.net/t/mavenization-and-debugging-in-eclipse/1020 ImageJ Forum Thread 1020]:

Underscores are needed if your plugin is an ImageJ 1.x style plugin. I.e.: does it implement <code>ij.plugin.PlugIn</code> or <code>ij.plugin.filter.PlugInFilter?</code> Then put an underscore in your JAR file and/or in your class name.

If you write an ImageJ2 command plugin (i.e.: implement the <code>org.scijava.command.Command</code> interface, with an <code>@Plugin</code> annotation) then the underscore is no longer necessary.

== Make the plugins appear in the menus ==

Example, which sets the <code>plugins.dir</code> property so that the plugin appears in the menus when launched from an IDE:

* {{GitHub | org=imagej | repo=minimal-ij1-plugin | commit=780286866ee67ffdc6506217c3f25d9a0ac15f6d | source=Process_Pixels.java#L176-L180 | label=Process_Pixels.java}}

Setting <code>plugins.dir</code> is only necessary for ImageJ 1.x style plugins. If you write an ImageJ2 command, it should appear in the menus regardless.

== NetBeans: what is the difference between Ant and Maven? Or: <code>build.xml</code> vs. <code>pom.xml</code> ==

From [http://stackoverflow.com/a/15122181 StackOverflow #15121928]:

<blockquote>
Ant is a build tool primarily, this means it knows how to compile and package source code and run tests, but has no ability to manage project dependencies. Ant uses build.xml files to define where to find the source code and which steps to take to build your project.

Maven is more than just a build tool, it is a project management tool. It allows you to define dependencies in the <code>pom.xml</code> proect definition, as well build, test and distribute the application. It also allows sub projects, parent projects and there exist many plugins for many other features. Maven will automatically download the dependencies and manages these dependencies between projects.
</blockquote>

Maven is declarative, whereas Ant is procedural. In Ant, you say "do X, then do Y, then do Z." Whereas in Maven, you say "my code is here, my resources are there, and please use these plugins." One advantage of the latter is that Maven provides a standardized build sequence (called the "build lifecycle") making it compatible with all the major IDEs.

== How to migrate an existing project to a Maven project? ==

'''In Netbeans'''       

Adapted from [http://stackoverflow.com/q/7548008 StackOverflow #7548008]:

* Backup your project.
* Create a new project with name <code>NewMavenProject</code>.
* Close your original project.
* Copy the <code>pom.xml</code> from {{GitHub | org=imagej | repo=minimal-ij1-plugin | label=imagej/minimal-ij1-plugin}} or other appropriate template.
* Modify the <code>pom.xml</code>'s project specific settings (e.g. project name, dependencies).
* Delete the <code>build.xml</code> and the whole <code>nbproject</code> folder.
* Move and rename the folder to <code>src/main/newproject</code> (<code>newproject</code> is the new name).
* Move <code>src/java</code> to <code>src/main/java</code>.
* Open your project again in NetBeans. It should be a Maven project now.
* Delete the unnecessary <code>NewMavenProject</code> project.
    

'''In IntelliJ IDEA'''    
* Create a new Maven Project with {{bc|File|New Project}}
* Select Maven on the left and click ''Next''
* Choose your custom GroupID (eg. com.yourwebsite) and an ArtifactID as single identifier for this project (eg. project_name) 
* Note that for ImageJ 1.x Plugins a "_" in the project name/ identifier is required for ImageJ 1.x Plugins
* The project structure required by Maven will be created for you
* For Git support (recommended): {{bc|VCS|Import into Version Control|Git}}
* Copy all <code>.java</code> files into ''[project_name]/src/main/java''
* Copy your <code>plugins.config</code> file into ''[project_name]/src/main/resources''
* In the main project directory ''[project_name]/'' you can find a <code>pom.xml</code> which has to be edited like the example shown in the previous chapter
* If your IDE asks to import all Maven changes you have to allow this
* Expand the Maven window which can be found on one of the edges in the IntelliJ window and select your project 
* Here you can right-click and ''Run Maven Build'' or alternatively press the green arrow above it in the Maven window to build your project
* The build process will generate two <code>.jar</code> file under ''[project_name]/targets/''

== Enable the ImageJ 1.x UI, instead of the ImageJ2 Swing UI ==

From [http://forum.imagej.net/t/ij1-or-ij2-style-for-plugin-development/1364/3 ImageJ Forum Thread 1364]:

Add the following dependency to your POM:

<source lang="xml">
<dependency>
  <groupId>net.imagej</groupId>
  <artifactId>imagej-legacy</artifactId>
  <scope>runtime</scope>
</dependency>
</source>

That will enable the ImageJ 1.x UI, instead of the ImageJ2 Swing UI which is otherwise the default.

== What is it all about with this Java 6 and Java 8 stuff? ==

From [http://forum.imagej.net/t/java3d-issue-bonej-with-latest-fiji-version-problem-solved/1151/6 ImageJ Forum Thread 1151]:

The current situation with respect to Java 6 vs. Java 8, as well as the ramifications there regarding Java 3D, is basically:

* If you download "vanilla" [[ImageJ2]] (author's note: in the context of software "vanilla" means software used as originally distributed without any customizations or updates applied to them) from the [[Downloads]] page, you get a "Java 8" version from February 2016.
* If you [[Fiji/Downloads|download the latest Fiji]] you get the newest "Java 8" version&mdash;i.e., with Java-8 update site. This includes the Java 3D 1.6 (SciJava fork) along with all Fiji plugins (except for [[TrakEM2]]) updated to work with it.
* If you [[Fiji/Downloads#Life-Line_Fiji_versions|download a Life-Line version of Fiji]] and fully update it, you'll have the newest (probably the final) "Java 6" version including the latest Java-6-compatible plugin versions. No Java 3D until you run the [[3D Viewer]] for the first time and it gets auto-installed. Those plugin versions are frozen: the ImageJ/Fiji developers are in the process of migrating everything to Java 8, and are only uploading new versions of everything to the Java-8 update site now, to avoid breaking the stable Java-6 versions of everything.

Ultimately, the ImageJ/Fiji developers will push all the Java-8 stuff back to the core ImageJ and Fiji sites. But not until the ImageJ/Fiji developers add a launch check that verifies your version of Java is new enough—and if not, tells you how to upgrade it. Ihe ImageJ/Fiji developers will definitely archive the final Java-6-compatible versions of ImageJ and Fiji when they complete that transition.

Note: You can check the Java version as [[Troubleshooting#Checking_the_Java_version|described here]].

More information can be read here: [[2015-12-22 - The road to Java 8]]

== Make a redistributable package from a locally customized Fiji ==

Turn your local customized Fiji into a redistributable package that can then be installed on other machines e.g. in your lab: use the [[Make Fiji Package]] command.

== Tests wit JUnit5 ==

In IntelliJ IDEA you may want to make sure that the JUnit5 Plugin is activated. The next step would be to append the following lines to your <code>pom.xml</code> file:

<source lang="xml">
<dependency> 
<groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter-engine</artifactId>
    <version>5.5.1</version>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>org.junit.platform</groupId>
    <artifactId>junit-platform-runner</artifactId>
    <version>1.5.1</version>
    <scope>test</scope>
</dependency>

<build>
    <plugins>
        <plugin>
            <!-- fix maven tests -->
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-surefire-plugin</artifactId>
            <version>3.0.0-M3</version>
            <configuration>
                <excludes>
                    <exclude>some test to exclude here</exclude>
                </excludes>
            </configuration>
        </plugin>
    </plugins>
</build>
</source>

'''Note:''' if you want to test GUI tests with TravisCI you have to activate a virtual display as described in the Travis CI chapter.

== Continuous Integration with Travis CI ==
If you want to share your plugin in the ImageJ updater automatically [[Automatic Update Site Uploads]], contribute to the ImageJ project [[Fiji/Contribution requirements]] or work in a team with multiple developers, you may want to build, test and deploy your Plugin with [[Travis CI]]. If you are hosting your code in a public [[GitHub]] repository this service is free for you. After signing in with your [[GitHub]] account you can activate single repositories for [[Travis CI]]. Travis then automatically clones your repository with every change and runs a build according to the <code>.travis.yml</code> configuration file in your root directory.

<source lang="yml">
	# specify compiler
	language: java
	sudo: false # faster builds
	jdk: openjdk8
	
	# maven build
	install: true
	script: mvn clean verify
	
	# cache maven dir for performance
	cache:
	  directories:
	    - $HOME/.m2
</source>

In case you are working with GUI tests, you may want to activate a virtual display as well:

<source lang="yml>
	# virtual display variable for gui tests
		dist: xenial
		services:
		  - xvfb
</source>

== JavaFX JAR not found ==

Add this to your pom.xml:
<source lang="xml">
    <build>
        <plugins>
            <!-- Fix JavaFX support -->
            <plugin>
                <groupId>com.zenjava</groupId>
                <artifactId>javafx-maven-plugin</artifactId>
                <version>8.8.3</version>
                <configuration>
                    <mainClass>your.package.with.Launcher</mainClass>
                </configuration>
            </plugin>
        </plugins>
    </build>
</source>

== Log4j warning in IntelliJ IDEA ==
Some of the tutorials seem to be missing a configuration file for Log4. IntelliJ will warn you about this as soon as you try to try to build the project:

<source>
log4j:WARN No appenders could be found for logger (org.bushe.swing.event.EventService).
log4j:WARN Please initialize the log4j system properly.
log4j:WARN See http://logging.apache.org/log4j/1.2/faq.html#noconfig for more info.
</source>

The missing config file is called <code>log4j.xml</code> and has to be located in  <code>.../src/main/resources/</code>:

<source lang="xml">
<!DOCTYPE log4j:configuration SYSTEM "log4j.dtd">
<log4j:configuration debug="true" xmlns:log4j='http://jakarta.apache.org/log4j/'>

    <appender name="fileAppender" class="org.apache.log4j.RollingFileAppender">
        <param name="File" value="demoApplication.log"/>
        <layout class="org.apache.log4j.PatternLayout">
            <param name="ConversionPattern" value="%d{yyyy-MM-dd HH:mm:ss} %-5p %c{1}:%L - %m%n" />
        </layout>
    </appender>

    <root>
        <priority value ="debug"></priority>
        <appender-ref ref="fileAppender"></appender-ref>
    </root>

</log4j:configuration>
</source>

[[Category:News]]
[[Category:ImageJ2]]