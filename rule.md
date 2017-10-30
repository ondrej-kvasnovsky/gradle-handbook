# Rule

Parameters can be usually passed via project or system variables. Rules can be used as replacement for tasks that should accept a parameter. For example -SourceSets are implemented using Rules.

### Script Rule

Lets create a task that pings a domain. We want to execute this task. Imagine other examples, where you would like to, for example, deploy system to various environments or publish an artifact to multiple repositories.

```
gradle pinggoogle.com
```

Here is the rule that will ping the google and save the result in a file.

```
ext {
    pingLogDir = "${buildDir}/reachable"
}
tasks.addRule('Rule Usage: ping<Hostname>') { String taskName ->
    if(taskName.startsWith('ping')) {
        task(taskName) {
            ext.hostname = taskName - 'ping'
            doLast {
                def url = new URL("http://${ext.hostname}")
                def logString
                try {
                    def pageContent = url.text
                    // Do some regex matching on pageContent
                    logString = "${new Date()}\t${ext.hostname}\tUP\n"
                    ext.up = true
                } catch(UnknownHostException e) {
                    logString = "${new Date()}\t${ext.hostname}\tUNKNOWN HOST\n"
                    ext.up = false
                } catch(ConnectException e) {
                    logString = "${new Date()}\t${ext.hostname}\tDOWN\n"
                    ext.up = false
                }
                file(pingLogDir).mkdirs()
                file("${pingLogDir}/ping.log") << logString
            }
        }
    }
}
```

### Object Rule

Here is the way to create rule inside a `buildSrc` folder.

```
import org.gradle.api.Rule

class HttpPingRule implements Rule {

  String getDescription() {
    'Rule Usage: ...'
  }

  void apply(String taskName) {
  }
}
```

We can use the rule like this. 

```
import org.gradle.examples.rules.HttpPingRule

tasks.addRule(new HttpPingRule(project))
```









