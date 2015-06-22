---
title: "Make the Lobby"
slug: making-the-lobby
---     

Now we'll make the lobby scene, which will be the first thing the user sees when the start the app. It will contain the Doodler logo, in addition to a single player and multiplayer button.

#Create a Lobby Class

> [action]
> 
> Create a new C++ class `Lobby` that inherits from `cocos2d::Node`. Have it `override` `init()` and `onEnter()` just like how `DrawingCanvas` did. Set up *Lobby.h* with the declarations and *Lobby.cpp* with empty implementations.
 
See if you can do it without looking at the solution. It's very common to create new classes that inherit from a Cocos2d-x base class, so you should try to get used to memorizing the steps required.

> [solution]
> 
Here's what your Lobby skeleton should look like:
>
*Lobby.h*
>
	#ifndef __Doodler__Lobby__
	#define __Doodler__Lobby__
>
	#include "cocos2d.h"
>
	class Lobby : public cocos2d::Node
	{
	public:
	    CREATE_FUNC(Lobby);
>	    
	protected:
	    bool init() override;
	    void onEnter() override;
>	    
	};
>
	#endif /* defined(__Doodler__Lobby__) */
>	
*Lobby.cpp*
>
	#include "Lobby.h"
>
	using namespace cocos2d;
>
	bool Lobby::init()
	{
	    if (! Node::init())
	    {
	        return false;
	    }
>	    
	    return true;
	}
>
	void Lobby::onEnter()
	{
	    Node::onEnter();
	}
	
#Add a Background
	
By default, the lobby will have a black background color. Let's change that by creating a background in `Lobby::init()`.

> [action]
> 
Add the background:
>
	LayerColor* background = LayerColor::create(Color4B(255, 255, 255, 255));
	this->addChild(background);
	
#Load the Lobby

The way we set things up, `DrawingCanvas` gets loaded and displayed first. Let's change it so that `Lobby` is displayed first. 

> [action]
> 
Open *HelloWorldScene.cpp*. Swap the `#include "DrawingCanvas.h"` at the top with `#include "Lobby.h"`.
>
Then, in `HelloWorld::init()` `create()` a new `Lobby` instead of `DrawingCanvas` and add that `lobby` as a child.

#Add Some Assets

We're going to need some assets for the logo and buttons in the lobby.

> [action]
Get started by [downloading the asset pack](Doodler-Assets.zip).
>
Save it in the *Resources* directory inside your project folder.
>
Double click *Doodler-Assets.zip* to unzip it.
>
It should look like this:
>
![Assets in Resources directory](assetsDoodler.png)
>
Drag the *resources* directories out of the *Doodler-Assets* directory, and into *res*. The result should look like this:
>
![Assets in res directory](assetsRes.png)

Feel free to delete Doodler-Assets.zip and the Doodler-Assets directory, as we now no longer need them.

#Set up Resolution-Dependent Asset Loading

The assets are now included in the Xcode project, which is great. But we need to tell Cocos2d-x which assets to load depending on what the screen resolution is.

> [action]
> 
Open *AppDelegate.cpp*. In `applicationDidFinishLaunching`:
>
Right below:
>
    director->setAnimationInterval(1.0 / 60);
>   
Add this:
>
	FileUtils::getInstance()->addSearchPath("res");
>
	cocos2d::Size targetSize = glview->getFrameSize();
>	 
	std::vector<std::string> searchResolutionsOrder(1);
>   
	if (targetSize.height < 481.0f)
	{
		searchResolutionsOrder[0] = "resources-1x";
	}
	else if (targetSize.height < 1335.0f)
	{
		searchResolutionsOrder[0] = "resources-2x";
	}
	else if (targetSize.height < 1921.0f)
	{
		searchResolutionsOrder[0] = "resources-3x";
	}
	else
	{
		searchResolutionsOrder[0] = "resources-4x";
	}
>    
	FileUtils::getInstance()->setSearchResolutionsOrder(searchResolutionsOrder);

The first line tells Cocos2d-x to search for assets in the *res* directory. That way, when we're loading the assets in code, we don't have to specify res every time, like "res/logo.png". Instead we can just specify "logo.png".

Next we figure out the screen size and assign that to `targetSize`. 

We create a `vector` called `searchResolutionsOrder` - in this vector we will specify the order that Cocos2d-x should search our resource folders to find matching assets. It's basically a fallback mechanism - say we need a 4x-sized "logo.png", but there isn't one in the *resources-4x* directory. If we specify multiple directories in the `searchResolutionsOrder` vector, then it can then search the next directory, like *resources-3x* instead.

In this case, however, we have assets for each resolution size, 1x through 4x. So we just specify one directory to find the assets in, based on the height of the screen.

The last line passes the `searchResolutionsOrder` vector to `FileUtls` so it can use it as it searches for assets.

#Add the Logo

We're going to create the logo sprite and buttons in a `Lobby` method called `setupUI()`.

> [action]
> 
Open *Lobby.h*. Declare a `protected` method called `setupUI()` with no parameters and return type `void`.
>
Open *Lobby.cpp* and create an empty implementation of `setupUI()`.

=

> [solution]
> 
	void Lobby::setupUI()
	{
>	    
	}

We're going to use *relative positioning* to make sure all of our UI looks good at all device resolutions. So instead of hardcoding a position, as in, "the logo will be at (200, 400)", we will instead ask the `Director` what the visible screen size is and compute a position based on that.

> [action]
So in `setupUI()`, let's figure out the screen size:
>
	Size visibleSize = Director::getInstance()->getVisibleSize();

Now let's make the Doodler logo that we shall display.

> [action]
> 
First, create the logo `Sprite` like this:
>
	Sprite* logo = Sprite::create("doodlerLogo.png");
>	
Next set the anchor point to the middle of the `Sprite`.
>
	logo->setAnchorPoint(Vec2(0.5f, 0.5f));
>	
Now we'll position the logo in the middle of screen's width, and 75% of its height.
>
    logo->setPosition(Vec2(visibleSize.width * 0.5f, visibleSize.height * 0.75f));
 >   
Finally, add the logo as a child of `Lobby`:
>
	this->addChild(logo);
>	
Now we need to add a call to `setupUI()` in `onEnter()`. Go ahead and do that.

Now try running it! You should see something like this:

![Logo in Lobby](lobbyFirstRun.png)


#Add Some Buttons

Okay, now it's time to add some buttons for the users to press. There will be a single player mode button - that one says "solo", and a multiplayer button that says "duo". 

These buttons will use *