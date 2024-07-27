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

## Progress with the windows version

I worked on the completing the input logic for the Jumping Ball application.
Figuring out character deletion was extremely easy. The only thing that I needed to do 
is to copy the `interiorTextbox.content` to a temporary variable with one less character.
an iteration with `strlen(interiorTextBox.content) - 1` was more than enough to do the job.

```cpp
 char cpyTemp[1024];

 for (int i = 0; i < strlen(interiorTextBox.content) - 1; i++)
 {
     cpyTemp[i] = interiorTextBox.content[i];
 }

 cpyTemp[strlen(interiorTextBox.content) - 1] = '\0';

 for (int j = 0; j < strlen(cpyTemp); j++)
 {
     interiorTextBox.content[j] = cpyTemp[j];
 }

 interiorTextBox.content[strlen(cpyTemp)] = '\0';
```

Also, I changed the instructions text to tell the user that the input value 
cannot be higher thann 100. To display everything properly, I had to add edit the 
font size too.

```cpp
appFont = TTF_OpenFont(fontResource.location, 50);
TTF_SetFontStyle(appFont, TTF_STYLE_BOLD);


DisplayText(    appRenderer, appFont, "Add a starting value under 100 and press start", 
                windowWidth * 0.065, windowHeight * 0.1);	

```



Unfortunately, the movement of the ball is not very realistic. I need to find a better 
implementation for its movement. At first, I thought it was a weird windows thing but
after testing the MacOS version, I found out that the problem exists on both platforms.