# Sprite Engine for Android Studio

This repository contains a simple implementation of a "sprite renderer" for Android Studio. Android Studio does not provide it's users with any innate sprite rendering/animation capabilities; it allows users to use the Bitmap, SurfaceView, and ImageView classes, however there is no package that contains a complete way to use spritesheets. This project is intended to provide users with that ability. If you would like a reference as to how the sprites are setup and rendered to the screen, see [Coding Android Sprite Sheet Animations](http://gamecodeschool.com/android/coding-android-sprite-sheet-animations/), which is a reference I used when writing the code for this project.

# What Is A "Sprite Engine"?

The term "sprite engine" refers to the use of spritesheets in order to implement animations or game development. An example of a spritesheet from this repositoy is shown below.

![Spritesheet](/app/src/main/res/mipmap-xxxhdpi/spritesheet_box_rotate_up_red_mirror.png)

If you would like to make a game or app that uses this form of spritesheet, clone this repository and start by editing the sample game provided for you. Don't forget to rename the package directories, and update the new package name inside of the manifest. For help on creating a game, descriptions of each class and its methods can be found below. Found on my GitHub are a total of four examples of using this engine to make games. These can be found under [Bunny's Massacre](https://github.com/pixarninja/bunny_massacre), [Pilgrim's Crossing](https://github.com/pixarninja/pilgrims_crossing), [World Forge](https://github.com/pixarninja/world_forge), and this repository.

# The Sample Game

![SampleGame](/source/Photoshop/sample_game_description.png)

# Rendering Scenes

This project utilizes many different objects to render a scene. In a general sense, there is a controllerMap variable that keeps track of all the SpriteController objects stored there. If you would like to render a character, button, or prop to the screen, you must create a new SpriteController, assign it an appropriate SpriteEntity object, and add it to the controllerMap. Each SpriteEntity object makes use of transitions (implemented inside of the refreshEntity() method) to decide the properties of the Sprite that will be rendered to the screen. For example, the SpriteButton's refreshEntity() method will swap from an "off" spritesheet to an "on" spritesheet when the button is pressed. The SpriteThread will draw the sprites to the screen in increments of a frame rate, which is set in the SpriteView.

# Class Definitions

## SpriteView

SpriteView is the class that forms the basis for rendering sprites on an Android device. The class extends SurfaceView, and implements all methods needed to make it a reputable View class. The SpriteView is responsible for keeping track of the SpriteThread (see below), and calling the onTouch() methods for each SpriteEntity in the hashmap of Controller objects.

### void initView()

Parameters: none. This method starts a new SpriteThread, and sets up what is needed for the surface of the SpriteView to be rendered. This includes implementing surfaceCreated, surfaceChanged, and surfaceDestroyed methods for the Holder of the SpriteView.

### void drawSprite()

Parameters: none. This method renders each SpriteEntity in the hashmap of Controller objects; objects are rendered from index 0 onwards, so the order at which they are added to the hashmap matters! This method should only be called from the SpriteThread whenever the time has met or surpassed the frame rate.

### boolean onTouchEvent(...)

Parameters: *MotionEvent event*. This method is activated whenever the screen is touched by the user. After the user touches the screen, this method activates the onTouchEvent for each SpriteEntity stored in the hasmap of Controller objects. This implementation can be editted to call each SpriteEntity's onTouchEvent() method with specific flags or other information. See Android's [MotionEvent documentation](https://developer.android.com/reference/android/view/MotionEvent.html) for more information on how to edit this function.

## SpriteController

SpriteController is the object that holds an animation's position, sprite, and frame rate variables. The role of the SpriteController is to manage which spritesheet is shown for each sprite; sprites are able to change the spritesheet displayed through transitions, and in order to save memory only one SpriteEntity object is created per controller. Thus, the controller stores the entity currently being displayed on the screen, and also keeps track of the position and frame rate for the sprites it manages.

### void makeTransition(...)

Parameters: *String ID*. This method calls the refreshEntity(ID) method on the controller's entity.

### void printController()

Parameters: none. This method prints information about the controller, for debugging purposes.

## SpriteEntity

SpriteEntity is one of the main classes that is used by this project. It provides the foundation of all objects rendered to the screen; the SpriteProp, SpriteButton, and SpriteCharacter classes all extend the SpriteEntity class. Inside this class are the following methods:

### static Bitmap decodeSampledBitmapFromResource(...)

Parameters: *Resources res, int resId, int reqWidth, int reqHeight*. This method is used to scale Bitmap images depending on the required width and hieght passed in.

### static int calculateInSampleSize(...)

Parameters: *BitmapFactory.Options options, int reqWidth, int reqHeight*. This is also used to scale Bitmap images, but is used to actually calculate the necessary sample size.

### void refreshEntity(...)

Parameters: *String ID. This method should be overridden and filled with transitions*. For examples on transitions, see how the sample game provides overrides to this method. See also the Transitions section of this document.

### void updateView()

Parameters: none. This method can be overrided to proved extra implementation. The intrinsic implementation of this class is to 1) move the animation if x delta and y delta are nonzero, 2) update the sprite frame being rendered, 3) update the bounding box for the sprite.

### void updateBoundingBox()

Parameters: none. This method updates the spritesheet's positing on the screen and the bounding box, which controls how the user interacts with the sprite.

### void getCurrentFrame()

Parameters: none. This method updates the sprite frame being rendered. This method is used to create movement for the animation.

### String parseID(...)

Parameters: *String ID*. This method adds functionality for parsing the transition to be rendered. This allows transitions to be inherited from or initialized, giving more control and dynamism. For examples on dynamic transitions, see the overriden refreshEntity method in the BoxRed class.

### void onTouchEvent(...)

Parameters: *SpriteView spriteView, LinkedHashMap.Entry<String, SpriteController> entry, LinkedHashMap<String, SpriteController> controllerMap, boolean touched, float xTouchedPos, float yTouchedPos*. This method is called when the user touches the screen. This method is initially left empty, since not all animations will need interactions. Different interactions have been modeled to create the sample game.

# Transitions

Spritesheets are read by using the Rect class in Android Studio; the implementation for parsing through the spritesheet is contained in the getCurrentFrame() method inside of the SpriteEntity class. This project has several different ways of implementing spritesheet transitions, these are covered below.

## Idle

By identifying a spritesheet as "idle", the SpriteController has the ability to return to that animation when necessary. The animation will loop, meaning once all frames have been parsed, the animation will start from the beginning and continue.

## Loop

By giving a Sprite the ID of "loop", the animation will loop; this functionality is described above.

## Once

By giving a Sprite the ID of "once", the animation will play one time and then transition to the "idle".

## Mirror

By giving a Sprite the ID of "mirror", the sprite controller will play the animation one time regularly, and then play the animation backwards until the first frame. The animation then stops. Delays can be specified to allow the animation to pause before playing backwards.
