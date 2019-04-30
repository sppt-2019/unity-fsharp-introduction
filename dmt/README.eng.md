
## Introduction
First we want to thank you for participating in our experiment. We want to start with clearafying the experiment does not test, the person doing them or their skill as a programmer, but rather how suited F# and C# is to game development.

Epic Games’ Tim Sweeney [[1]](https://groups.csail.mit.edu/cag/crg/papers/sweeney06games.pdf), and id Software’s John Carmark[[2]](http://www.gamasutra.com/view/news/169296/Indepth_Functional_programming_in_C.php) have long sugested to use functional programming in the game development world.

F# has gotten af reputition for being a golden middleroad - between the object-orientetet and functional paradigme [[3]](https://fsharpforfunandprofit.com/posts/fsharp-is-the-best-enterprise-language/). Everything as a basis is _immutable_, but if it needed things can be made _mutable_. Functions is _pure_ as long as you don't use _mutable_ variables.

In total there is 7 excercises in this survey, but it is not sure that you will solve all the excercises. Half of the time will be spent solving the excercises in F# and the rest of the time will be used on C#. The most important part is you get to try using both F# and C#.

You can find a [sheet with examples from F#](https://sppt-2019.github.io/unity-fsharp-introduction/), that you can use to get inspiration to the excercises. You are free to ask if you have any questions about the excercises or if you feel you get stuck.

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

## Excercise 1
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

## Opgave 2
#### Skydning del 1 - `Player` - _Events, Input_
- Tilføj et event på `Player`-klassen som bliver _triggered_ når der trykkes knappen `Fire1`
- Tilføj en _handler_ til eventet som printer "Fired weapon!" med `Debug.Log`
  - Vi tilføjer flere handlers i de senere opgaver

<br />
<br />
<br />

___
<br />
<br />
<br />

## Opgave 3
#### Skydning del 2 - `Player` - _Events, Prefab, Audio_
- Tilføj et felt af type `GameObject` på `Player`-klassen som kan bruges fra Unity Editoren, og sæt den til at indeholde `Bullet`-prefabben
- Tilføj en _handler_ der afspiller lyden `Shoot` når der skydes

<br />
<br />
<br />

___
<br />
<br />
<br />

## Opgave 4
#### Skydning del 3 - `Player` - _Events, Instantiation, Vector math_
- Tilføj en _handler_ som _instantierer_ `Bullet`-prefabben når der skydes
  - Brug `Bullet`-prefabben fra feltet fra opgaven før
- Tilføj kode til scriptet på `Bullet`-prefabben, som gør at den bevæger sig fremad/op mod fjenderne
  - Hastigheden skal kunne sættes fra Unity Editoren
- I `Start()`-metoden på `Bullet`-klassen, kald `GameObject.Destroy(this.gameObject, 8.0f)` så den bliver fjernet igen efter et antal sekunder, når den er ude af banen
HINT: _foran spilleren_ kan udtrykkes som `this.transform.position + Vector3.up`

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

