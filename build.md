# Build

### Build lifecycle

There are more events to listen in [BuildListener](https://docs.gradle.org/current/javadoc/org/gradle/BuildListener.html).

```
gradle.buildFinished {
    println "Build finished"
}
```

We can access build settings in the build file.

```
gradle.settingsEvaluated { Settings settings ->
    logger.info "settingsEvaluated"
    logger.info settings
}
```

Keep in mind that there are 3 phases during build:

1. Initialization
2. Configuration
3. Execution

`settingsEvaluated` is called during Initialization phase.

### Deamon mode

Deamon mode is started by default. We can disable it for task execution. Also, we can stop the deamon if needed.

```
gradle myTask --daemon
gradle myTask --no-daemon
gradle --stop
```

### Partial build

We can tell Gradle to not to rebuild project in order to speed up the build time. That might be valid for project with a lot of modules.

```
gradle myTask --no-rebuild
```

Also, we can only build the dependents.

```
gradle :sub-module:buildDependents
```

###  Upgrading Gradle

When upgrading Gradle version, it is probably wise to try out the new version first \(something like dry run to verify build outputs\). There is a plugin `compare-gradle-builds` that compares outputs from Gradle project build. That way we can check what the new version of Gradle is doing to our project.

```
apply plugin: 'compare-gradle-builds'
compareGradleBuilds {
    sourceBuild {
        projectDir = rootProject.projectDir
        gradleVersion = 4.1
    }
    targetBuild {
        projectDir = rootProject.projectDir
        gradleVersion = 4.2
    }
}
```

### Embedded web container

If we would like to provide embedded web container using Gradle, we can use `gretty` plugin. 

```
buildscript {
    repositories {
        jcenter()
    }

    dependencies {
        classpath 'org.akhikhl.gretty:gretty:+'
    }
}

apply plugin: 'org.akhikhl.gretty' // allows to run in an embedded web container

```

### Ant integration

We can include Ant build file into Gradle file. If there are no conflicts in task names, the Ant tasks are available via Gradle.

Lets assume we have this Ant file: `ant/build.xml`.

```
<project name="MyProject" default="dist" basedir=".">
    <description>
        simple example build file
    </description>
    <!-- set global properties for this build -->
    <property name="src" location="src"/>
    <property name="build" location="build"/>
    <property name="dist" location="dist"/>

    <target name="init">
        <!-- Create the time stamp -->
        <tstamp/>
        <!-- Create the build directory structure used by compile -->
        <mkdir dir="${build}"/>
    </target>

    <target name="compile" depends="init"
            description="compile the source">
        <!-- Compile the java code from ${src} into ${build} -->
        <javac srcdir="${src}" destdir="${build}"/>
    </target>

    <target name="dist" depends="compile"
            description="generate the distribution">
        <!-- Create the distribution directory -->
        <mkdir dir="${dist}/lib"/>

        <!-- Put everything in ${build} into the MyProject-${DSTAMP}.jar file -->
        <jar jarfile="${dist}/lib/MyProject-${DSTAMP}.jar" basedir="${build}"/>
    </target>

    <target name="cleanThatDoesNotConflictWithGradleCleanTask"
            description="clean up">
        <!-- Delete the ${build} and ${dist} directory trees -->
        <delete dir="${build}"/>
        <delete dir="${dist}"/>
    </target>
</project>
```

Then we can include this build.xml file in Gradle.

```
ant.importBuild 'ant/build.xml'
```

Then we can check what tasks were added into Gradle build.

```
gradle tasks --all
```

### Maven

We can migrate Maven project to Gradle. Go into Maven project folder and run `gradle init`. Gradle will try to migrate Maven project to Gradle. Gradle will migrate the basic stuff like dependencies. Maven plugins wont be migrated, this needs to be don manually.

