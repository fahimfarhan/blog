---
layout: post
title:  "Install JavaFx 11 in Linux"
description: How to install JavaFx 11 on Linux
date:   2019-07-28 21:03:36 +0600
categories:
---

Hello internet! Today we'll see how to install JavaFx 11 in Linux. This is the easiest way that I've found. 

## Install SDKMAN
SDKMAN is a package manager, just like python pip or php composer, but for java. Fire up your terminal, head over to their  [website](https://sdkman.io/install) and follow the instructions precisely to install sdkman.
Here are the instructions for your convenience:
```
$ curl -s "https://get.sdkman.io" | bash
$ source "$HOME/.sdkman/bin/sdkman-init.sh"
$ sdk version
```
## Install JavaFx
In your terminal, type :
```
$ sdk list java
```
This will show you a list of tentative candidates along with their corresponding version number. In my case, I can see somethig like this:
```
               |     | 7.0.232      | zulu    |            | 7.0.232-zulu        
               |     | 6.0.119      | zulu    |            | 6.0.119-zulu        
 Azul ZuluFX   |     | 11.0.2       | zulufx  |            | 11.0.2-zulufx       
               |     | 8.0.202      | zulufx  |            | 8.0.202-zulufx      
 BellSoft      |     | 12.0.2       | librca  |            | 12.0.2-librca       
               |     | 11.0.4       | librca  |            | 11.0.4-librca  
```
The important thing is to look for the `FX` suffix. So in this case at the time of writing, we have `Azul ZuluFX` with the `FX` suffix. So I'll be going with that. But you should select the one that you see.
```
$ sdk install java 11.0.2-zulufx
```
And that's it! You are all good to go! Fire up your favourite IDE and build awesome JavaFX apps!
