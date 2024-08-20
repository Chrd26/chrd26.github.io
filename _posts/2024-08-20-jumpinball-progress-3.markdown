---
layout: post
title: Jumping Ball
subtitle: Progress Report 3
categories: jumping-ball devlog
banner:
    image: "/assets/images/banners/Jumping_Ball.jpg"
    opacity: 1
    background: "#000"
    height: "100vh"
    min_height: "38vh"
    heading_style: "font-size: 4.25em; font-weight:bold;"
    subheading_style: "color: white"
tags: jumping-ball progress-report
top: 1
sidebar: []
---

## Working on MacOS and Windows Versions

I updated the MacOS source to code use char arrays instead of char* to fix memory problems.
On the Windows side, I created post-build event commands to copy library files and assets to the 
build folder and updated the additional library location option in the project properties.

Setting up post-build events was very easy.

1. Opened the project properties and then we click the **Build Events** option to find a list of the event types.
2. Added following shell commands to the **Post-Build Event's** command option.

```shell
copy "$(SolutionDir)SDL3.dll" "$(TargetDir)"
copy "$(SolutionDir)liblocated.dll" "$(TargetDir)"
copy "$(SolutionDir)SDL3_ttf.dll" "$(TargetDir)"
copy "$(SolutionDir)SDL3.lib" "$(TargetDir)/Resources/Libs"
copy "$(SolutionDir)liblocated.lib" "$(TargetDir)/Resources/Libs"
copy "$(SolutionDir)SDL3_ttf.lib" "$(TargetDir)/Resources/Libs"
copy "$(SolutionDir)fonts\Montserrat-VariableFont_wght.ttf" "$(TargetDir)/Resources/Fonts"
```
**It is important to note that the copy command does not create any directories. Any directories must 
be created before the copy commands are executed. Otherwise errors will occur.**

![This is a screenshot of Visual Studio showing how the shell commands have been set up in the post-build event option.](/assets/images/jumping-ball-screenshot-for-blog-2.png)

To let the program know where to find the library files, I needed to update the **Additional Library Directories** 
with the **Resources/Libs** directory.

![This is a screenshot of Visual Studio showing the updated additional library option with the new directory.](/assets/images/jumping-ball-screenshot-for-blog-1.png)

The files are copied to their corresponding directories as soon as the build is compelte.

![This is a screenshot of the Windows file explorer that shows all the files neatly placed in the target directory](/assets/images/jumping-ball-screenshot-for-blog-3.png)

Next, I need to create a setup project for the application. As soon as the setup app is complete, I will be able to move on building the 
Linux version.