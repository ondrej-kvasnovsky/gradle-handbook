# Dependencies Cleanup

As time goes on, we add some libraries. As time goes on and on, we update code and stop using some libraries. Then our dependencies are becoming messy, because we include more libraries than we need.

### Preview dependency tree

Lets say we have multi module project that consists from the following modules.

```
.
├── app
├── core
├── db
├── build.gradle
├── README.md
```

The app module, which is a directory, contains runnable application and we want to see all dependencies in all the configurations. We can do the following to dependencies for all classpaths \(or better said, configurations of classpaths\).

```
gradle :app:dependencies
```

When we are dealing with bigger project, we might want to get dependency for a specific classpath configuration. For example, the application is running fine, but when we run tests, something does not compile. In that case, we want to check `testCompile` configuration.

```
gradle :app:dependencies --configuration testCompile
```

Or if everything compiles fine, but tests are failing in runtime, we might want to check testRuntime configuration.

```
gradle :app:dependencies --configuration testRuntimeClasspath
```

### Gradle Lint

We can use Gradle [lint](https://github.com/nebula-plugins/gradle-lint-plugin) plugin by Netflix to cleanup dependencies in our Gradle project.

We need to apply the lint plugin and then define rules for the plugin. It is always to go with `all-dependency` rule.

```
buildscript {
    repositories { jcenter() }
    dependencies {
        classpath 'com.netflix.nebula:gradle-lint-plugin:latest.release'
    }
}

apply plugin: 'nebula.lint'

gradleLint.rules = ['all-dependency']
```

When we run the build, we will get report. It can look like this.

```
This project contains lint violations. A complete listing of the violations follows.
Because none were serious, the build's overall status was unaffected.

warning   duplicate-dependency-class         com.google.code.findbugs:annotations:3.0.1u2 in configuration ':compile' has 35 classes duplicated by com.google.code.findbugs:jsr305:3.0.1 (use --info for detailed class list) (no auto-fix available)

warning   duplicate-dependency-class         com.google.code.findbugs:annotations:3.0.1u2 in configuration ':compile' has 4 classes duplicated by net.jcip:jcip-annotations:1.0 (use --info for detailed class list) (no auto-fix available)

warning   duplicate-dependency-class         com.google.code.findbugs:annotations:3.0.1u2 in configuration ':testCompile' has 35 classes duplicated by com.google.code.findbugs:jsr305:3.0.1 (use --info for detailed class list) (no auto-fix available)

warning   duplicate-dependency-class         com.google.code.findbugs:annotations:3.0.1u2 in configuration ':testCompile' has 4 classes duplicated by net.jcip:jcip-annotations:1.0 (use --info for detailed class list) (no auto-fix available)

warning   unused-dependency                  this dependency is unused and can be removed build.gradle:46 compile 'org.yaml:snakeyaml:1.18'

warning   unused-dependency                  this dependency is unused and can be removed build.gradle:47 compile 'com.google.code.findbugs:jsr305:3.0.1'

warning   unused-dependency                  this dependency is a service provider unused at compile time and can be moved to the runtime configuration build.gradle:48 compile 'com.helger:ph-commons:8.0.0'
```

First we can try to do the automatic cleanup.

```
gradle
```

Now we need to go to Gradle build file and do the cleanup manually.

