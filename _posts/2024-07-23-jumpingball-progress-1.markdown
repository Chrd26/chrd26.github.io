---
layout: post
title: Jumping Ball
subtitle: Progress Report 1
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

## Working on the windows version

Just as expected. More weird character string memory problems. It is unfortunate that I have to rewrite the code
that manages character input and deletion. After some experimentation, I managed to fix the character input.
I deleted most memory allocations and used character arrays instead. The arrays are very easy to use compared to 
`char*`.
<br>
<br>

To fix the bug, I used temporary variables and loops to edit the input content while the user inputs new values. Also, I made sure that
the terminating character is properly added to the end of the string. **The program does not need 1024 characters. I need
to fix this on the next update.**

**main.c**
```c
char *newCharacter = events.text.text;
char cpyTemp[1024];

if (strlen(interiorTextBox.content) == 0)
{
     for (int i = 0; i < strlen(newCharacter); i++)
     {
        interiorTextBox.content[i] = newCharacter[i];
     }
}
else
{
    for (int i = 0; i < strlen(interiorTextBox.content); i++)
    {
        cpyTemp[i] = interiorTextBox.content[i];
    }

    cpyTemp[strlen(interiorTextBox.content)] = newCharacter[0];
    cpyTemp[strlen(interiorTextBox.content) + 1] = '\0';

    for (int k = 0; k < strlen(cpyTemp); k++)
    {
        interiorTextBox.content[k] = cpyTemp[k];
    }

    interiorTextBox.content[strlen(cpyTemp)] = '\0';
}
```

There was a need to edit the `textbox.c` source file too. The cursor of the text box
appears at the end of the input. The temp variable is used to display the user's input on the screen.

**textbox.c**
```c
char temp[5];

if (strlen(interiorTextBox.content) > 0)
{
    for (int i = 0; i < strlen(interiorTextBox.content); i++)
    {
        temp[i] = interiorTextBox.content[i];
    }

    temp[strlen(interiorTextBox.content)] = 'I';
    temp[strlen(interiorTextBox.content) + 1] = '\0';
}
else
{
    temp[0] = 'I';
    temp[1] = '\0';
}
```

I still need to fix the logic for the character deletion. Avoiding memory allocation is a good way to increase performance and headaches compared to 
using `char*`, `calloc`, and `malloc`. I should move the new code to the main working branch when it's finished.