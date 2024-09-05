---
layout: post
title: Jumping Ball
subtitle: Progress Report 4
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

## Completing the Windows version

Creating an installer for my program was elementary. I only needed to install an additional extension for Visual Studio. 
The extension is named [Visual Studio Installer Projects](https://marketplace.visualstudio.com/items?itemName=VisualStudioClient.MicrosoftVisualStudio2017InstallerProjects).
To create the installer, I used the following guide on YouTube:
<br>
<br>
<iframe width="560" height="315" src="https://www.youtube.com/embed/NOkBUoP54b8?si=FXXG3QePAcd7Ln7-" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
<br>
<br>

**I do need to say that finding a simple solution was a nice change of pace.**
<br>
<br>
<iframe width="560" height="315" src="https://www.youtube.com/embed/_zApRNv4fng?si=mjtp6E1OYuxNA6bJ" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
<br>
<br>

### Simulation Fixes

The simulation looked a bit weird on Windows and I decided to remove the deltaTime value from the operation. Now, the 
ball gets slow down by the value of the gravity and not the value of gravity multiplied by deltaTime.

```c
if (executionResults.upwardsMovement) 
{
    ball.y -= (double)executionResults.operatedVelocity;
    executionResults.operatedVelocity -= (int)executionResults.gravity;  
}else
{
    ball.y += (double)executionResults.operatedVelocity;
    executionResults.operatedVelocity += (int)executionResults.gravity; 
    printf("%d", ball.y);

    if (ball.y > windowHeight - ball.radius)
    {
        executionResults.operatedVelocity = 0;
    }
}
```

The simulation runs much smoother now! Time to start working on the Linux version.

