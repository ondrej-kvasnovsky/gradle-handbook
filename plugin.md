# Plugin

Plugins can extend other plugins. For example, `war` plugin extends `java` plugin. That means, we can remove `apply plugin: 'java'` if we are using `war` plugin.

### Script plugin

Script Plugins are plugins that live in a `.gradle` file and can be imported in other gradle build files.

We can create a plugin using local script file. In order to create a new script plugin, create a file `scriptPlugin.gradle` and put there the plugin code.

```
// scriptPlugin.gradle
task myScriptPluginTask(description: 'Testing script plugins', group: 'Testing') {
    if (project.hasProperty('myName')) {

        inputs.property('myName', myName)
        inputs.file file('gradlew')

        doLast {
            println "Hi there $myName :) "
            println file('gradlew').text
        }
    }
}
```

Then we can import that plugin in our build.gradle file.

```
apply from: 'scriptPlugin.gradle'
```

We can execute the task from the script plugin.

```
gradle myScriptPluginTask -PmyName=Ondrej 
```

### Object plugin

Object plugin can live in its own Gradle project, in src folder. Or the object plugin can be placed into buildSrc if it is supposed to live next to project.

We need to add the internal dependencies into Gradle build.

```
// enable writing gradle tasks in buildSrc
apply plugin: 'groovy'
dependencies {
    compile gradleApi()
    compile localGroovy()
}
```

Lets create a object plugin. First create the plugin class and put this class into `buildSrc/src/main/groovy`.

```
package com.test

import org.gradle.api.Plugin
import org.gradle.api.Project

class MyPlugin implements Plugin<Project> {

    @Override
    void apply(Project project) {
        // add plugin extension
        def extensionName = 'mySuperPlugin'
        def extension = project.extensions.create(extensionName, MyPluginExtension)
        // gradle myTaskFromPlugin
        project.task('myTaskFromPlugin') {
            doLast {
                println extension.myName
            }
        }
    }
}
```

To try out plugin extensions, create another class, next to the `MyPlugin` class.

```
package com.test

class MyPluginExtension {
    String myName = 'Default message...'
}
```

Then create a new properties file in `resource` folder `META-INF/gradle-plugins/myplugin.properties` that declares name of the plugin. Name of this file says what is the name of the plugin.

```
# assigns meaningful name to a plugin
implementation-class=com.test.MyPlugin
```

If we don't create the myplugin.properties file, we would have to apply

```
apply plugin: com.test.MyPlugin
```

But because we provided the properties file, we can reference the plugin by its name.

```
apply plugin: 'myplugin' // loaded using META-INF/gradle-plugins/myplugin.properties
```

Before we continue, we can configure the plugin using the plugin extension.

```
// set value of extension
mySuperPlugin {
    myName = 'Value from gradle.build is passed into plugin via an extension'
}
```

### IDE integration

We can use IDE plugins to generate project files \(to avoid project IDE files committed in source code repository, like Git\).

```
allprojects {
    apply plugin: 'eclipse'
    eclipse {
        project {
            name = 'Learning Gradle'
            // you can even generate web.xml using this
        }
    }

    apply plugin: 'idea'
    idea {
        project {
            jdkName = '1.8'
            module {
                downloadSources = true
                downloadJavadoc  = true
            }
        }
    }
}
```



