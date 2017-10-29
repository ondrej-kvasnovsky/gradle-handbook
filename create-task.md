# Create Task

Lets create 'Hello world' type of task. 

```
task helloWorld {
    doLast {
        println "Hello World!"
    }
}
```

When we run `gradle tasks --all`, we should be able to see that task among other project tasks. 

```
Other tasks
-----------
helloWorld
```

We can try to execute the task and see what happens. The task prints out 'Hello world' message in console. 

```
➜  gradle helloWorld

> Task :helloWorld
Hello World!


BUILD SUCCESSFUL in 0s
1 actionable task: 1 executed
```

## Tasks using Ant



## Dynamic tasks







