<span style="float: right">🇩🇰/<b><a href="/unity-fsharp-introduction/dmt/index.eng.html">🇬🇧</a></b></span>

## Introduktion
Først og fremmest tak for din deltagelse i dette eksperiment. Vi vil starte med at understrege at eksperimentet ikke tester dine evner som programmør, men derimod hvor egnet F# og C# er til spiludvikling

Epic Games’ Tim Sweeney [[1]](https://groups.csail.mit.edu/cag/crg/papers/sweeney06games.pdf), og id Software’s John Carmark [[2]](http://www.gamasutra.com/view/news/169296/Indepth_Functional_programming_in_C.php) har længe opfordret til funktionel programmering i spiludviklingsverdenen


F# har fået ry for at være nær den gyldne middelvej - mellem det objekt-orienterede og funktionelle paradigme [[3]](https://fsharpforfunandprofit.com/posts/fsharp-is-the-best-enterprise-language/). Alting er som udgangspunkt _immutable_, men hvis der er brug for det kan de gøres _mutable_. Funktioner er _pure_ så længe man holder sig fra at bruge _mutable_ variable


Der er i alt 7 opgaver i sættet, men det er ikke sikkert at du når at løse alle opgaverne. Halvdelen af tiden bliver brugt på at løse opgaver i F# og resten bliver brugt på C#. Det vigtigste er at du når at prøve kræfter med både F# og C#

Du kan finde et [ark med eksempler fra F#](https://sppt-2019.github.io/unity-fsharp-introduction/) og et [ark med eksempler fra C#](https://sppt-2019.github.io/unity-fsharp-introduction/csharp), som du kan bruge som inspiration til opgaverne. Spørg endelig hvis du er i tvivl om hvad opgaverne går ud på eller hvis du føler at du er gået i stå

For at kunne bruge F# i unity skal man bruge en [Unity-pakke](https://sppt-2019.github.io/unity-fsharp-introduction/#brug-af-f-i-unity), som tilføjer menuen vist på billedet herunder. **Dog er pakken allerede installeret i de projekter du skal arbejde i idag**

<img src="../tasks/images/fsharp-menu.png" style="display:block;margin: 0 auto; width: 40%;">

_Når du har lavet ændringer i F# filerne skal du **huske at kompilere** det ved at klikke på `Compile F#` eller ved at trykke på <kbd>F6</kbd> i Unity_

Til at redigere F# filer anbefaler vi [Visual Studio Code](https://code.visualstudio.com/)

Unity projekterne som skal bruges til opgaverne kan findes på GitHub, hvor du kan downloade/klone dem:
- [F# Unity projekt](https://github.com/sppt-2k19/unity-testing/archive/fsharp.zip)
- [C# Unity projekt](https://github.com/sppt-2k19/unity-testing/archive/csharp.zip)

Vores plugin kræver `dotnet` SDK
- [Download til Windows](https://dotnet.microsoft.com/download/thank-you/dotnet-sdk-2.2.203-windows-x64-installer)
- [Download til macOS](https://dotnet.microsoft.com/download/thank-you/dotnet-sdk-2.2.106-macos-x64-installer)
- [Installer på Linux](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu16-04/sdk-current)

**Når du har løst en opgave, skal du besvare en sektion i spørgeskemaet**
- [Link til spørgeskema](https://forms.gle/ejBNj3tRth6QNncm9)
  - Dit ID udleveres

<br />
<br />
<br />
<br />
<br />
<br />
<br />


## Opgave 1
#### Basal bevægelse - `Player` - _Input_
- Lav et script som bevæger dit **rumskib** venstre og højre, når der trykkes på <kbd>A</kbd>/<kbd>D</kbd> og <kbd>←</kbd>/<kbd>→</kbd>
  - Spilleren skal ikke kunne gå udenfor skærm-området
  
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
- Tilføj et event på `Player`-klassen som bliver _triggered_ når der trykkes knappen: `Input.GetButtonDown("Fire1")`
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
- Tilføj en _handler_ der afspiller lyden `Shoot` når der skydes. Der er allerede en `AudioSource` på Player prefabben

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
  - Det fjendtlige skib der bliver ramt skal også forsvinde (`Enemy`-klassen). Der er allerede en BoxColidder2D på alle enemies. 

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
#### At tabe - `Enemy` - _Behaviour, state-management_
- Tilføj kode så fjenderne bevæger sig fra side til side, synkront
  - Du kan f.eks. bruge et statisk event eller et _enemies_-objekt
- Når de er nået ud til en af siderne skal de, udover at skifte retning, rykke én unit ned (Vector3.down)
- Når de er nået helt ned til bunden af skærmen, skal man tabe spillet, og der skal skiftes til scenen `Dead`

<div class="invader-crop">
  <video src="space-invader-4.webm" style="display:block;margin:auto;max-width:60%" muted autoplay loop></video>
</div>

