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

We can configure copy in many ways.

```
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
}
```



