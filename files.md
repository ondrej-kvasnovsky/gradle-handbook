# Files

### Copy Task

We can define what files should be copied, excluded, included and where these should end up.

```
task copyMyFiles(type: Copy) {
   from 'myfiles'
   into 'build/copied'
   exclude '**/*test*'
   include '**/*.sh'
}
```

### More complex copy task

We can configure copy in many ways. We can include many directories, files and do many manipulations in single task. 

```
def dataContent = copySpec {
     from 'src/data'
     include '*.data'
 }
 
task initConfig(type: Copy) {
   from('src/main/config') {
       include '**/*.properties'
       include '**/*.xml'
       filter(ReplaceTokens, tokens: [version: '2.3.1'])
   }
   from('src/main/config') {
       exclude '**/*.properties', '**/*.xml'
   }
   from('src/main/languages') {
       rename 'EN_US_(.*)', '$1'
   }
   into 'build/target/config'
   exclude '**/*.bak'

   includeEmptyDirs = false

   with dataContent
   
   rename { fileName ->
       "production-file${(fileName - 'file-template')}"
  }
}
```

### Templates

Copy and transform a file. expand method uses SimpleTemplateEngine from Groovy. 

```
versionId = '1.6'

task fillInTemplate(type: Copy) {
  from 'source'
  include 'config.properties'
  into 'build/war/WEB-INF/config'
  expand([
    databaseHostname: 'db.company.com',
    version: versionId,
    buildNumber: (int)(Math.random() * 1000),
    date: new Date()
  ])
}
```

While config can look like this \(it is a template\). 

```
hostname: ${databaseHostname}
appVersion: ${version}
locale: en_us
initialConnections: 10
transferThrottle: 5400
queueTimeout: 30000
buildNumber: ${buildNumber}
buildDate: ${date.format("yyyyMMdd'T'HHmmssZ")}
```

### Filtering file content

We can filter content of copied file, line by line. 

```
task filter(type: Copy) {
  into 'build/poems'
  from 'source'
  include 'todo.txt'
  rename { it - '.txt' + '.html'}
  filter { line ->
   line.toUpperCase()
  }
}
```

### File by file

We can do operations for each file we are coping. 

```
task files(type: Copy) {
  into 'build/deploy'
  from 'source'
  eachFile { fileCopyDetails ->
    // do something fileCopyDetails.file.text
  }
}
```



