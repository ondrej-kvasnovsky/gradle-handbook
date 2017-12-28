# Dependency Locking

Updating dependencies can break our code. If we use dynamic versioning, we want to make sure that specific versions of dependencies are used. If we decide to update versions, we want to do it manually, as a deliberate action. We want to have dependencies under control.

Imagine you are a developer on project which is using dynamic versions. Every time you refresh dependencies, you might get different results, because someone could release, for example, a new minor version of a library the project uses. This can be really disturbing and slowing all the development down.

We need to lock versions and we can do it using Gradle [gradle-dependency-lock-plugin](https://github.com/nebula-plugins/gradle-dependency-lock-plugin) plugin.

```
buildscript {
    repositories {
        mavenLocal()
        jcenter()
    }

    dependencies {
        classpath 'com.netflix.nebula:gradle-dependency-lock-plugin:4.+'
    }
}

apply plugin: 'java'
apply plugin: 'nebula.dependency-lock'

repositories {
    mavenLocal()
    jcenter()
}

dependencies {
    compile 'com.google.guava:guava:14.+'
    testCompile 'junit:junit:4.+'
}
```

Here is how to create the dependency lock. 

```
./gradlew generateLock saveLock
```

Read the usage [documentation](https://github.com/nebula-plugins/gradle-dependency-lock-plugin/wiki/Usage) for more information.

