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

Doodler will have a 

Create a new *Header File* (*File > New > File*) and name it *Constants.h.*.

It's very common to declare new types and constant values that will are used by multiple classes in a single header file called *Constants* (or sometimes *Globals*). That way, multiple classes can use the same types without having to `#include` each other. This helps to maintain [separation of concerns](https://en.wikipedia.org/wiki/Separation_of_concerns). In our case, this new `enum` will be used by the `Character`, `Piece` and `MainScene` classes, but we don't want them all to have to `#include` each other unnecessarily.