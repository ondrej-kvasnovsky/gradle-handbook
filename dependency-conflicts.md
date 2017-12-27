# Dependency Conflicts

Dependency conflict happens when two libraries are present in a project. Gradle will make sure the latest version is picked by default. But what if our code is expecting an old version of a library? In this case, we can try to update our code and use the newer version. If that is doable, we are done.

But what if other library which we are using is dependent on the old version? We can't change the code in the old library. We have couple of options:

* try our luck and search for new version of library that would be using the new library 
* try to speak with people who are maintaining the library and kindly ask them to update version
* exclude the new dependency from that dependency
* force one version of library in whole project
* create [shaded jar](https://softwareengineering.stackexchange.com/questions/297276/what-is-a-shaded-java-dependency) where we create mapping between original and shaded dependency, then the files is moved to a specific folder inside a jar and byte code is modified to reference that moved classes of shaded jar

When we used to work with Maven and we faced to dependency conflicts, we used to call it [dependency hell](https://stackoverflow.com/questions/33907162/systematic-approach-with-maven-to-deal-with-dependency-hell). We could get a dependency tree: `mvn dependency:tree`. And then try to modify our POM and keep checking the dependency tree if the solution was found. This was very time and energy consuming.

What could we do when we use Gradle?

### Fail when there is a dependency conflict

Good defensive strategy is to fail when there is a dependency conflict in our project.

```
configurations.compile.resolutionStrategy {
    failOnVersionConflict()
}
```

### Exclude one of the conflicted dependencies

Another way to get away is to exclude dependencies from a library that brings bad version of library. First we run `gradle -q dependencies` to find where is the conflict.

One disadvantage is that the code becomes less maintainable. Imagine we want to increase version of `spring-core` from 5.0.0 to 5.1.0. What if they removed commons-logging and we won't know about it? The dependency definition becomes messy because everybody will be afraid to remove that `exclude` statement.

```
dependencies {
    compile('org.springframework:spring-core:5.0.0') {
        exclude name: 'commons-logging'
    }
}
```

### Force a specific version

If there are many libraries that bring a bad versions of the same library, we can force a certain version to be used in the whole project. Excluding dependencies will make sure there is not a specific version, forcing a certain version of library will make sure there is certain version. It means, if we don't want to use the latest version, we can force Gradle to use older version. This version will be forced including transitive dependencies.

```
configurations.compile.resolutionStrategy {
    force 'org.slf4j:slf4j-api:1.7.24'
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

### Remove transitive dependencies

We can get rid of all transitive dependencies that would come from a library. 

```
dependencies {
    compile('org.springframework:spring-core:5.0.0') {
        transitive = false
    }
}
```

### Shaded dependencies

We can solve dependency conflict by creating a shaded dependencies. There is [shadow](https://github.com/johnrengelman/shadow) Gradle plugin to help us. The shadow plugin has to features:

* create executable jar
* avoid dependency conflicts by moving libraries

_TODO: example_

