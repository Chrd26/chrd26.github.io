---
layout: post
title: Rebuild Back Better 
subtitle: Progress Report 1
categories: rebuild-back-better devlog
banner:
    image: "/assets/images/banners/RebuildBackBetter.jpeg"
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

# Rebuild Back Better
## Progress Report 1
### Upgrade Buttons

Upgrades are a very important feature for any clicker game that exists. They give a sense of acomplishment and progress during gameplay.
I had a hard time finding a good layout for the user interface of the game. In the end, I decided to just go for it and change things that I do not like.

I created a new header and a source file to acommodate the code of the upgrade button. Then, I added its logic.

**upgradebutton.h**
```cpp
class UpgradeButton
{
private:
	float x, y;
	float width, height;
	float verticalPOS;
	float maxVerticalMovement;
	SDL_Surface *upgradeButtonImageDefault;
	SDL_Surface *upgradeButtonImageHovered;
	SDL_Surface *upgradeButtonImageClicked;
	SDL_Renderer *renderer;

public:
	UpgradeButton(  std::string defaultImageLocation, std::string hoveredImageLocation,
					std::string clickedImageLocation, SDL_Renderer *getRenderer, 
					int _x, int _y, int _maxVerticalMovement);
					
	~UpgradeButton();
	bool IsButtonHovered(int getMouseX, int getMouseY);
	void DisplayButton(int mouseX, int mouseY, bool isButtonClicked);
};
```
<br>
<br>

**upgradebutton.cpp**
```cpp
bool UpgradeButton::IsButtonHovered(int getMouseX, int getMouseY)
{
	if (getMouseX >= x && getMouseX <= x + width)
	{
		if (getMouseY >= y && getMouseY <= y + height)
		{
			return true;
		}
	}
	return false;
}

UpgradeButton::UpgradeButton(   std::string defaultImageLocation, std::string hoveredImageLocation,
				                std::string clickedImageLocation, SDL_Renderer *getRenderer, 
				                int _x, int _y, int _maxVerticalMovement)
{
	renderer = getRenderer;
	auto loadDefaultImage = std::async(    std::launch::async, IMG_Load, 
								            defaultImageLocation.c_str());
	auto loadHoveredImage = std::async(    std::launch::async, IMG_Load,
								            hoveredImageLocation.c_str());
	auto loadClickedImage = std::async(    std::launch::async, IMG_Load,
								            clickedImageLocation.c_str());
										
	loadDefaultImage.wait();
	loadHoveredImage.wait();
	loadClickedImage.wait();
	
	upgradeButtonImageDefault = loadDefaultImage.get();
	upgradeButtonImageHovered = loadHoveredImage.get();	
	upgradeButtonImageClicked = loadClickedImage.get();
	
	if (upgradeButtonImageClicked == nullptr || upgradeButtonImageDefault == nullptr || upgradeButtonImageHovered == nullptr)
	{
		std::cout << "Failed to load a button" << std::endl;
		exit(-1);
	}
	
	x = _x;
	y = _y;
	maxVerticalMovement = _maxVerticalMovement;
								
}

void UpgradeButton::DisplayButton(int mouseX, int mouseY, bool isButtonClicked)
{
	SDL_Surface *convertedImage;
	float sizePercentage = 0.2;
	
	if (isButtonClicked)
	{
		convertedImage = SDL_ConvertSurface(  upgradeButtonImageDefault,
								              upgradeButtonImageDefault->format);
							
	}
	else if (IsButtonHovered(mouseX, mouseY))
	{
		convertedImage = SDL_ConvertSurface(  upgradeButtonImageHovered,
								              upgradeButtonImageHovered->format);
		
		if (verticalPOS < maxVerticalMovement)
		{
			verticalPOS += 20;
		}
	}
	else
	{
		convertedImage = SDL_ConvertSurface(  upgradeButtonImageDefault,
								              upgradeButtonImageDefault->format);
		verticalPOS = 0;
	}
	
	SDL_Texture *buttonImageTexture = SDL_CreateTextureFromSurface(renderer, convertedImage);
	const SDL_FRect buttonImageHolder = {  static_cast<float>(x),
								           static_cast<float>(y) - verticalPOS,
								           static_cast<float>(convertedImage->w),
								           static_cast<float>(convertedImage->h)};
											
	width = convertedImage->w;
	height = convertedImage->h;		
											
	SDL_RenderTexture(renderer, buttonImageTexture, nullptr, &buttonImageHolder);
	SDL_DestroyTexture(buttonImageTexture);
	SDL_DestroySurface(convertedImage);			
}

UpgradeButton::~UpgradeButton()
{
	SDL_DestroySurface(upgradeButtonImageDefault);
	SDL_DestroySurface(upgradeButtonImageHovered);
	SDL_DestroySurface(upgradeButtonImageClicked);
}
```
<br>
<br>
The button's texture changes based on its condition.Also, I am using the `VerticalPOS` and `maxVerticalMovement` variables to 
control it's animation. The animation is played when the button is hovered. 
<br><br>
Then, I created 2 more files create the interface of button in a type of grid. I named the files 'upgradegrid.h' and `upgradegrid.cpp`. The purpose of the files
is to generate the buttons and handle their states. It might be a better idea to create a new class for each upgrade and use 
some type of tag.

**upgradegrid.h**

```cpp
class Upgrades{
	
public:
	Upgrades(  std::string defaultImageButtonLocation, std::string hoveredImageButtonLocation,
               std::string clickedImageButtonLocation, int getWindowWidth, int getWindowHeight,
               float initialX, float initialY, float gap, SDL_Renderer *getRenderer);
				
	~Upgrades();
	void ShowUpgradeButtons(int _x, int _y, bool _isClicked);
	
private:
	static UpgradeButton *upgradeWorkers;
	static UpgradeButton *summonWorkers;
	static UpgradeButton *upgradeFighters;
	static UpgradeButton *summonFighters;
	static UpgradeButton *upgradeCursor;
};
```
<br>
<br>

**upgradegrid.cpp**

```cpp
#include "upgradegrid.h"
UpgradeButton *Upgrades::summonFighters = nullptr;
UpgradeButton *Upgrades::summonWorkers = nullptr;
UpgradeButton *Upgrades::upgradeCursor = nullptr;
UpgradeButton *Upgrades::upgradeFighters = nullptr;
UpgradeButton *Upgrades::upgradeWorkers = nullptr;

Upgrades::Upgrades( std::string defaultImageButtonLocation, std::string hoveredImageButtonLocation,
				    std::string clickedImageButtonLocation, int getWindowWidth, int getWindowHeight,
				    float initialX, float initialY, float gap, SDL_Renderer *getRenderer)
{
	summonFighters = new UpgradeButton(	defaultImageButtonLocation, 
								        hoveredImageButtonLocation,
								        clickedImageButtonLocation,
								        getRenderer,
								        initialX,
								        initialY,
								        150);
	
	summonWorkers = new UpgradeButton(	defaultImageButtonLocation,
										hoveredImageButtonLocation,
										clickedImageButtonLocation,
										getRenderer,
										initialX + gap,
										initialY,
										150);
										
	upgradeFighters = new UpgradeButton(	defaultImageButtonLocation,
											hoveredImageButtonLocation,
											clickedImageButtonLocation,
											getRenderer,
											initialX + gap * 2,
											initialY,
											150);
											
	upgradeWorkers = new UpgradeButton(	defaultImageButtonLocation,
										hoveredImageButtonLocation,
										clickedImageButtonLocation,
										getRenderer,
										initialX + gap * 3,
										initialY,
										150);
										
	upgradeCursor = new UpgradeButton(	defaultImageButtonLocation,
										hoveredImageButtonLocation,
										clickedImageButtonLocation,
										getRenderer,
										initialX + gap * 4,
										initialY,
										150);									
														
}

Upgrades::~Upgrades()
{
	delete(upgradeWorkers);
	delete(upgradeFighters);
	delete(upgradeCursor);
	delete(summonFighters);
	delete(summonWorkers);
}

void Upgrades::ShowUpgradeButtons(int _mouseX, int _mouseY, bool _isClicked)
{
	summonFighters->DisplayButton(_mouseX, _mouseY, _isClicked);
	summonWorkers->DisplayButton(_mouseX, _mouseY, _isClicked);
	upgradeFighters->DisplayButton(_mouseX, _mouseY, _isClicked);
	upgradeWorkers->DisplayButton(_mouseX, _mouseY, _isClicked);
	upgradeCursor->DisplayButton(_mouseX, _mouseY, _isClicked);
}
```
<br>
<br>
To make everything work, I updated the gameplay and main files.

**gameplay.h**

```cpp
public:
	static Upgrades *upgrades;
```
<br>
<br>

**gameplay.cpp**
```cpp
upgrades = new Upgrades(	defaultUpgradedButtonImageLocation, 
							hoveredUpgradeButtonImageLocation,
							clickedUpgradeButtonImageLocation,
							windowWidth,
							windowHeight,
							static_cast<float>(windowWidth * 0.22),
							static_cast<float>(windowHeight * 0.9),
							200,
							getRenderer	);
```

The only thing, is to update the gameplay class on the main file to instantiate the upgrade buttons:

**main.cpp**

```cpp
std::string generateBackgroundImagePath = Game::execpath + std::string("/Contents/Resources/graphics/enviroment/Game_Enviroment_Alternative_Sky.jpg");
							std::string generateMenuSfxPath = Game::execpath + std::string("/Contents/Resources/audio/menulightup/lightup.wav");
							std::string generateUpgradeButtonDefaultPath = Game::execpath + std::string("/Contents/Resources/graphics/interface/UpgradeButton.png");
							std::string generateUpgradeButtonClickedPath = Game::execpath + std::string("/Contents/Resources/graphics/interface/UpgradeButtonClicked.png");
							std::string generateUpgradeButtonHoveredPath = Game::execpath + std::string("/Contents/Resources/graphics/interface/UpgradeButtonLightUp.png");
							Game::gameplay = new Gameplay(	generateFont2Path,
															generateBackgroundImagePath,
															generateMenuSfxPath,
															Game::windowWidth,
															Game::windowHeight, 
															Game::renderer,
															generateUpgradeButtonDefaultPath,
															generateUpgradeButtonHoveredPath,
															generateUpgradeButtonClickedPath	);
											
```

# Result

<iframe width="560" height="315" src="https://www.youtube.com/embed/iyMon8DLn7s?si=jBXILl0VN4LO-n7u" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
