# Testing

We are going to use Java plugin in this example to demonstrate how to use Gradle for testing our code.

First we apply Java plugin and add dependency to JUnit.

```
apply plugin: 'java'

repositories {
    mavenCentral()
}

dependencies {
    testCompile 'junit:junit:4.12'
}
```

Then we can run all the tests: `gradle test`

Or we can run a single test using test property:

```
gradle :sub-module:test -Dtest=com.test.MyTest
```

### Separation of Unit and Integration tests

We might have multiple types of tests in our projects. Typically, it is unit, integration and functional tests. Putting all the files into test folder is the default strategy, that could be considered as messy. We might want to put our tests into separate folders and run each set of tests separately. Unit tests should not require any special configuration. But the other tests, like integration, might require to start up databases or services.

Lets create unit and integration tests in the next Gradle build script.

```
// set system properties for tests
test {
    // exclude integration tests
    exclude '**/*IntegrationTest.class'

    forkEvery = 5 // batch of 5, each processes will do 5 tests
    maxParallelForks = (int) Runtime.runtime.availableProcessors() / 2

    systemProperty 'hiThere', 'Hi :)'

    testLogging {
        showStandardStreams = true // turns on logging of standard and error output
        exceptionFormat 'full'
        events 'started', 'passed', 'skipped', 'failed' // to get info how much tests failed etc...
    }
}

apply plugin: 'announce'

test.beforeSuite { TestDescriptor suite ->
    println "beforeSuite called: $suite"
}

test.afterSuite { TestDescriptor suite, TestResult result ->
    logger.info 'afterSuite called!'
    announce.announce("What the hack?", 'local')
}

// run integration test in separate task
// gradle integrationTest
task integrationTest(type: Test) { // run integration test in separate task
    include '**/*IntegrationTest.class'
    reports.html.setDestination(file("$reports.html.destination/integration"))
    reports.junitXml.setDestination(file("$reports.junitXml.destination/integration"))
}
check.dependsOn integrationTest

// probably better to create separate folders for unit and integration tests
sourceSets {
    intTest {
        java.srcDir file('src/intTest/java')
        resources.srcDir file('src/intTest/resources')
        compileClasspath = sourceSets.main.output + configurations.testRuntime
        runtimeClasspath = output + compileClasspath
    }
}
task integrationTest2(type: Test) { // run integration test in separate task
    testClassesDir = sourceSets.intTest.output.classesDir
    classpath = sourceSets.intTest.runtimeClasspath
}
// run the tests in separate folder
// gradle integrationTest2
integrationTest2.beforeSuite { // gradle :sub-module:integrationTest2
    println "REALLY?" // place to startup a database or anything what needs to be started for integration tests
}
integrationTest2.afterSuite {
    println "REALLY" // place for cleanup
}
```

### Show logs and standard output in console

By default, all standard outputs are hidden. We can change it and see the printed output in the console while tests are running. 

```
test {
    testLogging {
        exceptionFormat = 'full'
        showStandardStreams = true
        events "started", "passed", "skipped", "failed", "standardOut", "standardError"
    }
}
```



