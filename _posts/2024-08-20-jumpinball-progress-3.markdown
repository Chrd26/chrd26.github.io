---
layout: post
title: Jumping Ball
subtitle: Progress Report 2
categories: jumping-ball devlog
banner:
    image: "/assets/images/banners/Jumping_Ball.jpg"
    opacity: 1
    background: "#000"
    height: "100vh"
    min_height: "38vh"
    heading_style: "font-size: 4.25em; font-weight:bold;"
    subheading_style: "color: white"
tags: rbb progress-report
top: 1
sidebar: []
---

## Working on MacOS and Windows Versions

I updated the MacOS version with the new code that I added on the Windows version. This 
should fix any potential memory problems. On the Windows side, I added post-build events to
move all the important files to the build folder. This is the first step of creating an executable 
that can be ran everywhere.

To set up post-build events is very simple. First, we open the project properties and then we click the
**Build Events** option to find a list of the event types. For the project I am currently 
working, I added the following shell commands to the **Post-Build Event's** command line option.

```shell
copy "$(SolutionDir)SDL3.dll" "$(TargetDir)"
copy "$(SolutionDir)liblocated.dll" "$(TargetDir)"
copy "$(SolutionDir)SDL3_ttf.dll" "$(TargetDir)"
copy "$(SolutionDir)SDL3.lib" "$(TargetDir)/Resources/Libs"
copy "$(SolutionDir)liblocated.lib" "$(TargetDir)/Resources/Libs"
copy "$(SolutionDir)SDL3_ttf.lib" "$(TargetDir)/Resources/Libs"
copy "$(SolutionDir)fonts\Montserrat-VariableFont_wght.ttf" "$(TargetDir)/Resources/Fonts"
```
**It is important to note that the folders need to already exist because the copy command won't 
create them and an error will occur.**

The commands copy the **.dll** to the target directory, the **.lib** files to **Resources/libs**, and 
the **font** to **Resources/Fonts**.

![This is a screenshot of Visual Studio showing how the shell commands have been set up in the post-build event option.](/assets/images/jumping-ball-screenshot-for-blog-1.png)

The application also, needs to find the library files in that are being copied to the **Resources/Libs** directory.
This was pretty easy. The only thing I had to do is add the directory to the **Additional Library Directories** in the 
**Project Properties**.

![This is a screenshot of Visual Studio showing the updated additional library option with the new directory.](/assets/images/jumping-ball-screenshot-for-blog-2.png)

Now, all the improtant files are being copied to the target directory move it wherever I want.

![This is a screenshot of the Windows file explorer that shows all the files neatly placed in the target directory](/assets/images/jumping-ball-screenshot-for-blog-3.png)

Next, I need to create a setup project for the application. As soon as the setup app is complete, I will be able to move on building the 
Linux version.