
## Introduction
First we want to thank you for participating in our experiment. We want to start with clearafying the experiment does not test, the person doing them or their skill as a programmer, but rather how suited F# and C# is to game development.

Epic Games’ Tim Sweeney [[1]](https://groups.csail.mit.edu/cag/crg/papers/sweeney06games.pdf), and id Software’s John Carmark[[2]](http://www.gamasutra.com/view/news/169296/Indepth_Functional_programming_in_C.php) have long sugested to use functional programming in the game development world.

F# has gotten af reputition for being a golden middleroad - between the object-orientetet and functional paradigme [[3]](https://fsharpforfunandprofit.com/posts/fsharp-is-the-best-enterprise-language/). Everything as a basis is _immutable_, but if it needed things can be made _mutable_. Functions is _pure_ as long as you don't use _mutable_ variables.

In total there is 7 exercises in this survey, but it is not sure that you will solve all the exercises. Half of the time will be spent solving the exercises in F# and the rest of the time will be used on C#. The most important part is you get to try using both F# and C#.

You can find a [sheet with examples from F#](https://sppt-2019.github.io/unity-fsharp-introduction/), that you can use to get inspiration to the exercises. You are free to ask if you have any questions about the exercises or if you feel you get stuck.

To be able to use F# in unity you have to use a [Unity-package](https://sppt-2019.github.io/unity-fsharp-introduction/#brug-af-f-i-unity), that adds a menu as shown in the picture below. **The package is already installed for this project you have to work with today**

<img src="../tasks/images/fsharp-menu.png" style="display:block;margin: 0 auto; width: 40%;">

_When you have made changes in a F# file you have to **remember to complie** it, by clicking on the `Compile F#` or by pressing <kbd>F6</kbd> in Unity_

Unity projects that you have to use for the excercises can be foud on GitHub, where you can download/clone them:
- [F# Unity project](https://github.com/sppt-2k19/unity-testing/archive/fsharp.zip)
- [C# Unity project](https://github.com/sppt-2k19/unity-testing/archive/csharp.zip)

The plugin requires `dotnet`
- [Download to Windows](https://dotnet.microsoft.com/download/thank-you/dotnet-sdk-2.2.203-windows-x64-installer)
- [Install on Linux](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu16-04/sdk-current)

**When you have solved a excercise, you have to answer a section in the questionarie**
- [Link to questionarie](https://forms.gle/K7mBzR8TGmD5SzMM7)
  - Your ID will be handed out

<br />
<br />
<br />
<br />
<br />
<br />
<br />

## Exercise 1
#### Basic Movement - `Player` - _Input_
- Make a script that moves your **spaceship** left and right, when the buttons are pressed <kbd>A</kbd>/<kbd>D</kbd> and <kbd>←</kbd>/<kbd>→</kbd>
  - The player should not be able to move outside the screen-area
  
<div class="invader-crop">
  <video src="space-invader-1.webm" style="display:block;margin:auto;max-width:60%" muted autoplay loop></video>
</div>

<br />
<br />
<br />

___
<br />
<br />
<br />

## Exercise 2
#### Shooting part 1 - `Player` - _Events, Input_
- Add a event on `Player`-class that gets _triggered_ when the button `Fire1` is pressed
- Add a _handler_ to the event that prints "Fired weapon!" with `Debug.Log`
  - We are adding more handlers in later excercises

<br />
<br />
<br />

___
<br />
<br />
<br />

## Exercise 3
#### Shooting part 2 - `Player` - _Events, Prefab, Audio_
- Add a field of type `GameObject` on `Player`-class that can be used from the Unity Editor, and make it contain the `Bullet`-prefab
- Add a _handler_ that can play the sound `Shoot` when you shoot

<br />
<br />
<br />

___
<br />
<br />
<br />

## Exercise 4
#### Shooting part 3 - `Player` - _Events, Instantiation, Vector math_
- Add a _handler_ that _instatiates_ the `Bullet`-prefab when you shoot
  - Use the `Bullet`-prefab from the field in the last exercise
- Add code to scripetet on the `Bullet`-prefab, that makes it move forward/up towards the enemies
  - The speed should be able to be set in the Unity Editor
- In `Start()`-method on `Bullet`-class, call `GameObject.Destroy(this.gameObject, 8.0f)` such that it is removed again after a number of seconds, when it exits the screen
HINT: _infront of the player_ can be expressed as `this.transform.position + Vector3.up`

<div class="invader-crop">
  <video src="space-invader-2.webm" style="display:block;margin:auto;max-width:60%" muted autoplay loop></video>
</div>

<br />
<br />
<br />

___
<br />
<br />
<br />

## Opgave 5
#### Ødelæggelse - `Bullet` & `Enemy` - _Collisions, Audio_
- Tilføj kode på `Bullet`-klassen der gør at skuddet forsvinder efter at have ramt et fjendligt skib
  - Det fjendtlige skib der bliver ramt skal også forsvinde (`Enemy`-klassen)

<div class="invader-crop">
  <video src="space-invader-3.webm" style="display:block;margin:auto;max-width:60%" muted autoplay loop></video>
</div>

<br />
<br />
<br />

___
<br />
<br />
<br />

## Opgave 6
#### At vinde - `Planet` - _Behaviour, state-management_
- Tilføj kode så `Planet`-klassen kan holde styr på hvor mange fjender der er tilbage vha. tagget **Enemy**
  - Når alle fjender er døde skal der skiftes til scenen `Done` 

<br />
<br />
<br />

___
<br />
<br />
<br />

## Opgave 7
#### At vinde - `Enemy` - _Behaviour, state-management_
- Tilføj kode så fjenderne bevæger sig fra side til side, synkront.
  - Du kan f.eks. bruge et statisk event eller et _enemies_-object
- Når de er nået ud til en af siderne skal de, udover at skifte retning, rykke én unit ned (Vector3.down)
- Når de er nået helt ned til bunden af skærmen, skal man tabe spillet, og der skal skiftes til scenen `Dead`

<div class="invader-crop">
  <video src="space-invader-4.webm" style="display:block;margin:auto;max-width:60%" muted autoplay loop></video>
</div>

