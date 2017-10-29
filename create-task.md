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

### Tasks using Ant

Ant is available in all Gradle builds.

```
task antUsed() {
    ant.echo(message: 'Ant called...')
}
```

### Dynamic tasks

Taks can be created in dynamic way.

```
3.times {
    task "task$it" {
        doLast {
            println "Dynamic task nr $it"
        }
    }
}
```

Run `gradle tasks --all` to get all the available tasks. We should be able to see dynamically created tasks there.

```
Other tasks
-----------
task0
task1
task2
```

### Task dependencies

Tasks can be dependent on other tasks. There can be no circular dependencies \(Gradle needs to be able to create an acyclic dependency graph\).

```
task1.dependsOn task0
task2.dependsOn task1
// task1.dependsOn task2 // circular dependency won't work in Gradle
task runWithAllDeps(dependsOn: task2)
```

### Task abbreviation

Almost all the tasks are executable by its abbreviation. If there is conflict in abbreviation uniqueness, Gradle fails to run the task.

```
task runWithAllDeps(dependsOn: task2) // (gradle rWAD) to run it by abbreviation
```

### Exclude task from execution

Use `-x` flag to say what tasks should be excluded from task execution.

```
gradle rWAD -x task0
```

### Task graph

We can hook into the task execution graph.

```
gradle.taskGraph.whenReady { graph ->
    println "All tasks in Directed Acyclic Graph:"
    println graph.allTasks
}
```



