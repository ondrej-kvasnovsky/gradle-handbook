# Dependencies

Before we start adding dependencies, we need to say what plugis are going to be used. For Java project, we first have to apply Java plugin and then we can provide dependencies.

```
apply plugin: 'java'
repositories {
    jcenter()
}
dependencies {
    compile 'org.slf4j:slf4j-api:1.7.25'
}
```

### Work with dependencies

We can takes all jars for given configuration and copies it into a specific folder. The following task is going to take all `compile` dependencies and copy it into to `copied` folder.

```
task copyDependenciesToLocalDir(type: Copy) {
    from configurations.getByName('compile').asFileTree
    into "copied"
}
```

We can then reuse all jar files in a folder and add them into a scope.

```
dependencies {
    compile fileTree(dir: 'copied', includes: '*.jar')
}
```

### Dependencies

Add dependency without its transitive dependencies.

```
dependencies {
    compile('org.springframework:spring-core:5.0.0') {
        transitive = false
    }
}
```

Dominate a certain version, including transitive dependencies, over others.

```
dependencies {
    compile('org.springframework:spring-core:5.0.0') {
        force = true
    }
}
```

Exclude a certain transitive dependency.

```
dependencies {
    compile('org.springframework:spring-core:5.0.0') {
        exclude name: 'commons-logging'
    }
}
```

Add jar into compile configuration.

```
dependencies {
  compile files('lib/hacked-vendor-module.jar')
}
```

Add all jars from a folder into compile configuration.

```
dependencies {
  compile fileTree('lib')
}
```

### Project dependencies

We can have dependencies on other sub-modules or aka projects.

```
dependencies {
  compile project(':codec')
  compile project(':content')
}
```

### Repositories

We can obtain dependencies from multiple repository types.

```
repositories {
    mavenLocal() // => MavenArtifactRepository.java
    ivy {} // => IvyArtifactRepository.java
    maven {} // => MavenArtifactRepository.java
    flatDir dirs: ["${projectDir}/lib"] // => FlatDirectory.java
}
```

### Offline

Gradle tries to connect to repositories to check if the dependencies are up to date. We can turn all interactions with network using `--offline` flag.

```
gradle clean build --offline
```

### Browse dependencies

We can create our own task to print all dependencies for a specific scope.

```
task printDeps {
    doLast {
        configurations.compile.each {
            println it
        }
    }
}
```

Other way to check dependencies is to use dependencies and dependencyInsight tasks that are provided by Gradle.

```
gradle -q dependencies
gradle -q dependencyInsight --dependency slf4j-api
```

### Dependency resolution

When using many libraries, there can be version conflicts of some libraries. We can force certain version of a library for our project.

```
configurations.compile.resolutionStrategy {
    force 'org.slf4j:slf4j-api:1.7.24'
}
```

If we want to be strict and we want to avoid version conflicts, we might want to say Gradle to fail build if there is a version conflict.

```
configurations.compile.resolutionStrategy {
    failOnVersionConflict()
}
```

### Refreshing dependencies

When working with SNAPSHOTs, we might want to force Gradle to reload dependencies.

We can refresh dependencies using --refresh-dependencies flag.

```
gradle --refresh-dependencies
```

Or we can change caching times.

```
configurations.compile.resolutionStrategy {
    cacheChangingModulesFor(0, TimeUnit.SECONDS)
    cacheDynamicVersionsFor(0, TimeUnit.SECONDS)
}
```

### Creating own configuration

We might need to create our own configuration \(a bucket\) to place our files \(dependencies\).This example shows how compile configuration can be created and used.

```
repositories {
  mavenCentral()
}
configurations {
  compile
}
dependencies {
  compile 'org.springframework:spring-core:3.0.5'
}
```

For example Java plugin provides six configurations: archives, default, compile, runtime, testCompile testRuntime.

Configurations can extend from other configurations.

```
configurations {
  myCompile.extendsFrom('compile')
}
```



