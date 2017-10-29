# Versioning

Lets do the following exercise to understand versining concepts. 

```
class ProjectVersion {
    Integer major
    Integer minor
    Boolean release

    String toString() {
        "$major.$minor${release ? '' : '-SNAPSHOT'}"
    }
}

ext.versionFile = file('version.properties')
task loadVersion { // this task is part of configuration phase, therefore, invoked before printVersion is executed
    // phases are: initialization => configuration => execution
    project.version = readVersion()
}

ProjectVersion readVersion() {
//    logger.quite 'reading the version file'
    def props = new Properties()
    versionFile.withInputStream {
        props.load(it)
    }
    new ProjectVersion(major: props.major.toInteger(), minor: props.minor.toInteger(), release: props.release.toBoolean())
}

task makeReleaseVersion(group: 'versioning', description: 'Makes project a release version') {
    inputs.property('release', version.release)
    outputs.file file('version.properties')
    doLast {
        version.release = true
        ant.propertyfile(file: versionFile) {
            entry(key: 'release', type: 'string', operation: '=', value: 'true')
        }
    }
}
task increaseMajorVersion(group: 'versioning') {
    inputs.property('major', version.major)
    outputs.file file('version.properties')
    doLast {
        ant.propertyfile(file: versionFile) {
            entry(key: 'major', type: 'int', operation: '+', value: 1)
        }
    }
}
// task rules - to make dynamic tasks
tasks.addRule("Pattern: increment<Classifier>Ver2: increments version") { String taskName ->
    if(taskName.startsWith("increment") && taskName.endsWith("Ver2")) { // gradle incrementMinorVer2 printVersion and gradle incrementMajorVer printVersion
        String classifier = (taskName - 'increment' - 'Ver2').toLowerCase()
        println classifier
        switch(classes) {
            case 'major': ++version.major
                break
            case 'minor': ++version.minor
                break
        }
        println version
        ant.propertyfile(file: versionFile) {
            entry(key: classifier, type: 'int', operation: '+', value: 1)
        }
    }
}

task printVersion {
    group = 'versioning'
    description = 'Helps with versioning'
    doLast {
        logger.quiet "Version: $version"
    }
}
build.finalizedBy printVersion // e.g., to define task to cleanup after some other task (e.g. build task)
```

Other way to create project versioning is using enhanced task. 

