---
title: "Make it pretty"
slug: make-it-pretty
---     

Now that we have the basics of the app down, it's time to polish the UI to make it friendly and fun to use.

But first, let's add some more functionality.

#Clear and Back Buttons

We're going to add a button to `DrawingCanvas` that will clear the canvas of any drawings, and another that will take the user back to the lobby.

> [action]
> 
First declare a new `protected` method called `void setupMenus()`. 
>
In the implementation of `setupMenus()`, get the visible size of the screen and store it in a variable called `visibleSize`. Look at your implementation of `onEnter()` if you forget how to do that.

<!--comment to break up action blocks-->

> [action]
> 
Next, create and add two new instances of `ui::Button*` with these properties:
>
<table>
	<thead>
		<tr>
			<th align="center">Button Name</th>
			<th align="center">Position</th>
			<th align="center">Texture</th>
			<th align="center">Pressed Texture</th>
			<th align="center">Callback Method Name</th>
		</tr>
		</thead>
		<tbody>
		<tr>
			<td>clearButton</td>
			<td>top right corner</td>
			<td>clearButton.png</td>
			<td>clearButtonPressed.png</td>
			<td>clearPressed</td>
		</tr>
		<tr>
			<td>backButton</td>
			<td>top left corner</td>
			<td>backButton.png</td>
			<td>backButtonPressed.png</td>
			<td>backPressed</td>
		</tr>
	</tbody>
</table>
>
Don't forget to also code the callback methods! For those, we only want to register the touch if it is `TouchEventType::ENDED`. Also don't forget to `#include "CocosGUI.h` in *DrawingCanvas.h*. You should have learned everything you need to do this yourself, so try not to look at the solution.

<!--html comment to break up boxes-->

> [solution]
> 
The code in `setupMenus()`:
>
    ui::Button* clearButton = ui::Button::create();
    clearButton->setAnchorPoint(Vec2(1.0f, 1.0f));
    clearButton->setPosition(Vec2(visibleSize.width, visibleSize.height));
    clearButton->loadTextures("clearButton.png", "clearButtonPressed.png");
    clearButton->addTouchEventListener(CC_CALLBACK_2(DrawingCanvas::clearPressed, this));
    this->addChild(clearButton);
>   
    ui::Button* backButton = ui::Button::create();
    backButton->setAnchorPoint(Vec2(0.0f, 1.0f));
    backButton->setPosition(Vec2(0.0f, visibleSize.height));
    backButton->loadTextures("backButton.png", "backButtonPressed.png");
    backButton->addTouchEventListener(CC_CALLBACK_2(DrawingCanvas::backPressed, this));
    this->addChild(backButton);
>
The callback methods:
>
	void DrawingCanvas::clearPressed(Ref* pSender, ui::Widget::TouchEventType eEventType)
	{
	    if (eEventType == ui::Widget::TouchEventType::ENDED)
	    {
>
	    }
	}
>
	void DrawingCanvas::backPressed(cocos2d::Ref *pSender, cocos2d::ui::Widget::TouchEventType eEventType)
	{
	    if (eEventType == ui::Widget::TouchEventType::ENDED)
	    {
>
	    }
	}

<!--html comment to break the boxes-->
> [action]
> 
Add a call to `setupMenus()` in `onEnter()` so that our buttons are created.

Your canvas should now look like this:

![Two buttons in simulator](addedButtons.png)

#Implement the Buttons

Making the buttons work is really simple. `DrawNode` already has a method to clear the canvas. So in `clearPressed()`, after checking that the touch type is `ENDED`, clear the `drawNode`:

	drawNode->clear();
	
The back button implementation is also one line - we'll actually change it later, but for now, add this in `backPressed()`:

	Director::getInstance()->popScene();

Test it out! It should work well.

<!--A video here would be nice-->

#Create Constants.h

Doodler will have fixed color palette: 5 colors and white. The user will be able to choose from the 5 colors to draw with, and the background color will be white.

We're going to define those colors in a new header file.

> [action]
> 
Create a new *Header File* (*File > New > File*) and name it *Constants.h.*. Make sure you check Doodler iOS and Doodler Mac as targets.

<!--html comment to separate blocks-->

> [info]
> 
It's very common to declare new types and constant values that will are used by multiple classes in a single header file called *Constants* (or sometimes *Globals*). That way, multiple classes can use the same types without having to `#include` each other. This helps to maintain [separation of concerns](https://en.wikipedia.org/wiki/Separation_of_concerns).
>
In this case, the colors are only used by by `DrawingCanvas`. But it's still useful to declare them in a separate file, because then we can change them easily. Whenever you find yourself using a [magic number](https://en.wikipedia.org/wiki/Magic_number_(programming)#Unnamed_numerical_constants), consider if it would make more sense for that number to live in a more accessible  *Constants* file. Magic numbers are also called *unnamed numerical constants*. It means a number that appears in the source code, without any indiciation of what it represents.

<!--html comment to separate blocks-->

> [action]
>
Inside the header guards of *Constants.h*, add the following variable definitions:
>
	#include "cocos2d.h"
>
	static const float INITIAL_RADIUS = 4.0f;
>
	static const cocos2d::Color4F COLOR_PURPLE =  cocos2d::Color4F(134/255.0f, 103/255.0f, 172/255.0f, 1.0f);
	static const cocos2d::Color4F COLOR_BLUE   =  cocos2d::Color4F(101/255.0f, 174/255.0f, 195/255.0f, 1.0f);
	static const cocos2d::Color4F COLOR_GREEN  =  cocos2d::Color4F(115/255.0f, 200/255.0f, 175/255.0f, 1.0f);
	static const cocos2d::Color4F COLOR_YELLOW =  cocos2d::Color4F(240/255.0f, 185/255.0f, 72/255.0f, 1.0f);
	static const cocos2d::Color4F COLOR_RED    =  cocos2d::Color4F(242/255.0f, 108/255.0f, 76/255.0f, 1.0f);
	static const cocos2d::Color4F COLOR_WHITE  =  cocos2d::Color4F(245/255.0f, 244/255.0f, 240/255.0f, 1.0f);
	
<!--html comment to separate boxes-->
	
> [action]
> 
Next go to *DrawingCanvas.cpp* and `#include "Constants.h"` at the top. Change the magic number `4.0` in `drawNode->drawSegment()` to instead be `INITIAL_RADIUS`. Next, in the `init()` method, where we create the background color, change this:
>
	background = LayerColor::create(Color4B(255, 255, 255, 255));
>	
to this:
>
	background = LayerColor::create(Color4B(COLOR_WHITE));
	
You may notice that `COLOR_WHITE` isn't pure white, it's actually slightly off-white. (RGB is 245, 244, 240 instead of 255, 255, 255). That's because we would like to create the look and feel of drawing on paper, and there's no real paper that is truly pure white.

> [action]
> 
Now do the same for `Lobby` - make sure the background uses `COLOR_WHITE` instead of pure white.

It's important in game design to be careful about the colors and art we use - we want to present a consistent look and feel to the user.

> [action]
> 
Try running it! Can you see the difference?

The difference between the off-white and pure white might not be immediately obvious. But when compared side-to-side, it should be:

![Pure white](pureWhite.png) 

![Off-white](offWhite.png)

Now can you see the difference?