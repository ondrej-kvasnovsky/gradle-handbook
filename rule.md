# Rule

Parameters can be usually passed via project or system variables. Rules can be used as replacement for tasks that should accept a parameter. 

Lets create a task that pings a domain. We want to execute this task. 

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



