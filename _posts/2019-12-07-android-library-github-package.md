---
layout: post
title:  "Android Library Github Package"
description: How to Publish an Android Library as a Github Package 
date:   2019-12-07 21:03:36 +0600
categories:
---
Hello there! In this post, we'll see how to publish an android library using github package.


## Create a Library
To begin with, first we need a demo project, let's call it MyAwesomeLibrary . For demonstration purposes, I am going to add a sample dependency named [alphamu/PinEntryEditText](https://github.com/alphamu/PinEntryEditText) to my library. Just add this line in the build.gradle file:
```
dependencies {
    implementation ('com.alimuzaffar.lib:pinentryedittext:2.0.6') 
    // ... ... your other dependencies
}
```
Basically dependency is a beautiful looking pin edit text. You may try creating a sample xml file in the main activity or any other convenient place.
Next, open the `build.gradle` file.  The first line is apply plugin: 'com.android.application', change it into apply plugin: 'com.android.library'.
## Generate Token
After that, go to your github account and navigate to Settings -> Developer Settings -> Personal Access Tokens -> Generate new token.
In the checklist, select write:packages, read:packages and Generate a token. You will see a token. This is the only time github will show you, so copy it and save it on a file on your computer for now. If you don't do it / lose it, basically it's gone for ever and so the only option is to regenerate/create a new token.
Once you have your token,  go to your library project, create a new file named github.properties (or any other name that is meaningdul to you) and add your github username (because the token was generated from it) and the token, so the file now looks something like this:
```
gpr.usr=YOUR_USER_NAME
gpr.key=YOUR_TOKEN
```

We don't want our credentials to be compromised. So open the .gitignore file, and add github.properties in it. Then create a github repository and upload the library codes in it.
## Edit build.gradle file
Now open the build.gradle file again, and add the following code in it:
```
apply plugin: 'maven-publish'. // Apply this plugin at the top of your library build.gradle

def githubProperties = new Properties()
githubProperties.load(new FileInputStream(rootProject.file("github.properties"))) //Set env variable GPR_USER & GPR_API_KEY if not adding a properties file

def getVersionName = { ->
 return "1.0.0" // Replace with version number
}

def getArtificatId = { ->
 return "MyAwesomwLibrary" // your library name
}

publishing {
 publications {
   bar(MavenPublication) {
      groupId 'com.yourcompany.anystring' // Replace with group ID/any meaningful string
      artifactId getArtificatId()
      version getVersionName()
      artifact("$buildDir/outputs/aar/${getArtificatId()}-release.aar")
    }
 }
 
repositories {
        maven {
               name = "GitHubPackages"
        /** Configure path of your package repository on Github
         ** Replace GITHUB_USERID with your/organisation Github userID       
         ** and REPOSITORY with the repository name on GitHub
        */
 url = uri("https://maven.pkg.github.com/GITHUB_USERID/REPOSITORY")
credentials {
        /** Create github.properties in root project folder file with     
         ** gpr.usr=GITHUB_USER_ID & gpr.key=PERSONAL_ACCESS_TOKEN 
         ** Set env variable GPR_USER & GPR_API_KEY if not adding a properties file**/
 
         username = githubProperties['gpr.usr'] ?: System.getenv("GPR_USER")
         password = githubProperties['gpr.key'] ?: System.getenv("GPR_API_KEY")
      }
    }
  }
}
```
## Publish Library
Finally, fire up your terminal and run the following commands:
```
$ cd path/to/MyAwesomeLibrary
$ gradle build		# this line generates the library as .aar file
$ gradle publish	# uploads the .aar file into github
```

## Use Your Library in other Project
Now that we have uploaded the library into github, we can use it.Let's create a new android project (say Prototype). Copy and paste the github.properties file that you created in the library into Prototype and add it into `.gitignore` file. After that, open the build.gradle file, and add edit it so that the file should look something like this:
```
apply plugin: 'com.android.application'
apply plugin: 'io.fabric'

/**Create github.properties in root project folder file with gpr.usr=GITHUB_USER_ID  & gpr.key=PERSONAL_ACCESS_TOKEN**/
def githubPropertiesFile = rootProject.file("github.properties");
def githubProperties = new Properties()
githubProperties.load(new FileInputStream(githubPropertiesFile))

android {
// ...
}
dependencies {
   // add myAwesomeLibrary
   implementation 'com.yourcompany.anystring:MyAwesomeLibrary:1.0.0'
   // implementation 'groupId:artifactId:versionNumber'
   // Add MyAwesomeLibrary's dependencies
   implementation ('com.alimuzaffar.lib:pinentryedittext:2.0.6') 
   // other dependencies
}
repositories {
        maven {
            name = "GitHubPackages"
            /*  Configure path to the library hosted on GitHub Packages Registry
             *  Replace UserID with package owner userID and REPOSITORY with the repository name
             *  e.g. "https://maven.pkg.github.com/enefce/AndroidLibraryForGitHubPackagesDemo"*/
             
            url = uri("https://maven.pkg.github.com/UserID/REPOSITORY")
credentials {
        /** Create github.properties in root project folder file with     
         ** gpr.usr=GITHUB_USER_ID & gpr.key=PERSONAL_ACCESS_TOKEN 
         ** Or set env variable GPR_USER & GPR_API_KEY if not adding a properties file**/
 
                username = githubProperties['gpr.usr'] ?: System.getenv("GPR_USER")
                password = githubProperties['gpr.key'] ?: System.getenv("GPR_API_KEY")
            }
        }
    }
```

Remember, you need to add implementation 'groubId:ArtifactId:versionNumber' , so whatever you add in the library should be added in this format. Also, if your library has its own dependencies (such as alphamu/PinEntryEditText in this example as mentioned before), you must add them as well. Otherwise, your code won't run.
Finally, hit the hammer icon to build your project and then run it. Enjoy!
## References:
1. [proandroiddev.com](https://proandroiddev.com/publishing-android-libraries-to-the-github-package-registry-part-1-7997be54ea5a)