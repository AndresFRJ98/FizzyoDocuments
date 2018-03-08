# Unity Game User Guide
A Unity package for Fizzyo game development

## Start making a Unity game
Start making a Unity game by opening Unity and click new project, and type the name for your game.

When you first open unity, you will see many tabs and menus. Take a look at each component. At the upper part of the window, you should see the tab “scene”. This is where your game is built mainly. In Unity, you create a game by using gameObjects. You can create and place gameObjects in the scene and attach scripts to control them. 
You can have multiple scenes in your game, e.g. a menu as one scene, clicking play goes to another scene.

Next to the “scene” tab you should see “game” and “console”. Game shows what your game looks like when its played, and console gives you messages, such as for errors, debug.

There are also other important tabs you will use.
Hierarchy - Shows list of gameObjects in the scene.
Project - list of game files
Inspector - Information about gameObjects.

Find a tab called project, right click on the **Assets** folder, and click create.
You should create 4 files in the assets folder:

-   **Prefab**, (such as for NPC, enemy sprites - game objects you reuse)
-   **Scenes**, (scenes are like levels, where you place your objects and build your game)
-   **Texture**, (images for your game)
-   **Scripts**. (scripts are attached to game objects to control their behaviour)

When creating your game, you should organise by placing images, scripts, etc that are used in the game, into appropriate folders.

## Using the Fizzyo Framework package

After downloading the file, either:
-   Import by selecting import assets by clicking Assets (on the top bar) > Import Package > Custom Package 
-   Or copy the Fizzyo file and paste it inside Assets folder in your Unity game file.

Once the Fizzyo Framework package is in your assets folder, it is ready to be used.
The Fizzyo Framework contains scripts which allows your game to be compatible with the Fizzyo device. The scripts are always accessed by singletons, therefore to use any of the script from the framework, all you need to do is in your script, use:

```csharp
FizzyoFramework.Instance. ~~~~
```

## Looking at the example game

Look at the example game from [here](https://github.com/zcabrmo/Fizzyo_Example_Game)

In the scenes file, there will be two scenes, Example game and the Tutorial game. The example game is a complete version and the tutorial game is for you to try and build.

The tutorial game already has some game objects in the scene. These are simple cubes which can be made from selecting GameObject > 3D object > Cube. To turn this scene into a (minimalistic) game, we will write scripts and attach them to the game objects.

As the player will supposedly play the game using a tablet and the device, therefore the controls of Fizzyo games should be not be complex.
The design of this game is - the player builds much energy as possible, as jump power, and jump using the power to aim the high score.

The control of this game is:

-   Breath Blow input from the device : Build up jump power
-   Button on the device : Release jump power energy to jump!!!

## Writing scripts

When developing a Fizzyo game, the very first thing you need to do is to add the “FizzyoFramework” to the scene. Whichever scene using the framework needs to have the “FizzyoFramework” script. 

To add “FizzyoFramework” to the script, you can attach it to a game object in the scene. Click “GameObject” at the top, and select  create empty. Select the empty game object you have created and rename it to FizzyoFramework. Click on Add Component. This allows to add new component to the game object, such as when you want to add new scripts. Go to script and add the FizzyoFramework script to the gameobject. You should have just one of the script in a scene.

Look at the “Cube(Player)” object. This is what the player will directly control using the Fizzyo device. Click on the object, and click on “Inspector”. This is where you can see information about the components attached to the game object. 

Select Add component > Physics > Rigidbody. This lets the cube to have the physics to move. For this game, the gravity should be turned on, and Is kinematic should be unticked.  Also select new script, C#, give it an appropriate name so you will know what the script will do, e.g. playerController. If you right click on the script in the inspector, at the bottom there is “Edit Script”. This takes you to the default editor. (You can also open the script with you prefered editor)

When you first open a new script, you will see this.

(img)

At the very top are **namespaces** which are group of classes you can use. By default you have “using UnityEngine”, “using System.Collections” etc.
When using the FizzyoFramework, the script needs to include the Fizzyo namespace, therefore write “using Fizzyo”

Below the namespace you will see the name of the class, and inside start and update.
 
**Start** is for initialising, this happens at the start when the object is in the scene.
**Update** runs the code inside it every frame of the game.

## Using the Fizzyo Device Button

Firstly, you can write a simple code using the framework.

Example:

```csharp
void Update () {
    	if (FizzyoFramework.Instance.Device.ButtonDown()) {
        	Debug.Log("button pressed");
    	}
```
By writing inside the update this code runs every frame of the game. When the button is pressed, in the console the log “button pressed” appears. The debug log may be useful when checking functionality of some code.

Checking the button works, write a code for the button in the game.

Example:

```csharp
void Start () {
    	rb = gameObject.GetComponent<Rigidbody>();
    }
void update() {
        	if (FizzyoFramework.Instance.Device.ButtonDown()) {
            	rb.AddForce(0, y, 0);
        	}
    }

```

Insert a number of your choice at y, save and press the play button in the unity editor.

At the start rb is initialised and assigned to the rigid body component of the game object. This allows the movement of the object to be controlled.

The FizzyoFramework.Instance.Device.ButtonDown() returns true if the button is pressed.
Using the if statement, rb.Addforce(0,y,0) happens when the button is pressed. The Addforce adds force to the object in the (x,y,z) direction.

## Using Fizzyo Breath Blow Input

When the player blows into the device, there are many variables that are handled by the framework, such as pressure, breath length, number of breath, number of good breath, etc.
For the simplicity of this particular game, just the pressure variable is used. Check the documentation on list of classes to see what you can use.

Earlier the button functioned to make the player game object jump upwards. There should also be breath input of the device affecting the game. The breath pressure builds up the energy for the jump power of the object. Longer and stronger breath will result in a higher jump.

To use pressure, use

```csharp
FizzyoFramework.Instance.Device.Pressure();
```

The code responsible for adding force to the object can be amended, by adding pressure as a multiplier to enhance the jump.

Example:

```csharp
void Update () {

    	pressure = FizzyoFramework.Instance.Device.Pressure();

    	jumpPower = jumpPower + pressure;

    	if (FizzyoFramework.Instance.Device.ButtonDown()) {
        	rb.AddForce(0, y * jumpPower, 0);
    	}
    }
}
```

## Recording the score

In Fizzyo games, game developers can code to submit highscores. And ofcourse to make a game more fun and lively, and competitive among players, the game should have a score / highscore system.

The score for this game can be recorded as the highest point reached by the cube.

Example:

```csharp
void Start () {
    	originalPos = gameObject.transform.position.y;
            }
    
	void update {
    	height = gameObject.transform.position.y - originalPos;

    	if (height > highest) {
        	highest = height;
        	score = (int)height;
    	}
   	 
	}

```
## Calibration scene

The Fizzyo device should be calibrated using the calibration scene which comes with the framework package. When building the game, this scene should be added in the build. By default the calibration scene appears when the game starts, but this can be disabled by unticking the box in the inspector.

Example in one: 

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Fizzyo;

public class TutorialExample : MonoBehaviour {

	Rigidbody rb;
	float jumpPower;
	float pressure;
	float originalPos;
	float height;
	float highest;
	int score;

    // Use this for initialization
    void Start () {
    	rb = gameObject.GetComponent<Rigidbody>();
    	originalPos = gameObject.transform.position.y;
    }
    
    // Update is called once per frame
    void Update () {

    	pressure = FizzyoFramework.Instance.Device.Pressure();

    	jumpPower = jumpPower + pressure;

    	if (FizzyoFramework.Instance.Device.ButtonDown()) {
        	rb.AddForce(0, 5 * jumpPower, 0);
    	}

    	height = gameObject.transform.position.y - originalPos;

    	if (height > highest){
        	highest = height;
        	score = (int)height;
    	}
   	 
	}
}

```