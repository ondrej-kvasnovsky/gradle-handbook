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



