# Installation

The easiest way to install Gradle is to use Brew. First, install Brew.

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

Then we can install Gradle.

```
brew install gradle
```

When we decide to update to latest version of Gradle, execute the following command. 

```
brew upgrade gradle
```

When working with Gradle project, we might want to freeze version of Gradle. Because other versions of Gradle might break the build. Put this into a `build.gradle` file. 

```
task wrapper(type: Wrapper) { // when 'gradle wrapper' is executed, it creates needed wrapper files
    gradleVersion = '4.2.1'
}
```

Then we run `gradle wrapper` in console and it generates gradle wrapper that ensure the project is build using the specified version.

