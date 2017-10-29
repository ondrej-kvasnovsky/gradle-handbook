# Artifacts

When we have applied Java plugin, we can run `gradle assemble` in order to create jar files in `build` directory. We can update the defaults. 

```
task sourcesJar(type: Jar) {
    baseName baseName
    classifier 'sources'
    from sourceSets.main.allSource
}
artifacts {
    archives sourcesJar // creates 'build/libs/myproject-sources.jar'
}
```

### Create distribution

Apply plugin distribution and then we can create different distribution archives. Here we create `main` distribution, that contains all jars. Then we create `docs` distribution that contains all source files. 

```
apply plugin: 'distribution'
distributions {
    main {
        baseName = baseName
        contents {
            from { libsDir }
        }
    }

    docs {
        baseName = "$baseName-docs"
        contents {
            from(libsDir) {
                include sourcesJar.archiveName
            }
        }
    }
}
```

### Publish artifacts

We can use `maven-publish` plugin to upload our artifacts into a repository.

```
apply plugin: 'maven-publish'
publishing {
    publications {
        plugin(MavenPublication) {
            from components.java
            artifactId 'published-file'
            version '1.0'
            // groupId needs to be provided in order to publish to artifactory
            groupId 'org.gradle.sample'
            artifact sourcesJar
        }
    }
    // specify own maven local repository
    repositories {
        maven {
            name 'myLocal'
            url "file://$projectDir/repo"
            // gradle publishPluginPublicationToMyLocalRepository <= check the name, name of maven configuration is in the name of the task!
        }
        // to publish into remote and secured artifactory, use:
        maven {
            name 'JFrog'
            url "http://jfrog.com/your/repo"
            // gradle publishPluginPublicationToJFrogRepository
            credentials {
                username = ''
                password = ''
            }
        }
    }
}

```



