# Create Project

The basic way to create Gradle project is to use command line. Create a new directory that is named as your project, step into that directory and call `gradle init` task.

```
mkdir hello-world
cd hello-world
gradle init
```

The `gradle init` task creates couple of files: 

* gradle.build - contains few predefinied dependencies, but they are all commented out
* gradle.settings - contains project settings, like name of the project
* gradle wrappers - that can be used to use a specific version of Gradle per project



