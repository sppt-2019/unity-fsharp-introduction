## Generel information om C# & Unity
C# er et objekt orienteret programmeringssprog som kører i .NET Platformen. Eftersom C# er Objekt orienteret betyder det at næsten alt betragtes som Objekter og funktionskald. Syntaksen i C# er inspireret af Smalltalk og C++ og er curly braces ( `{` og `}` ) baseret, istedet for indentations-baseret som vi kender det fra F#.

I C# er der er implicit membership. I C# vil det være typisk i et `GameObject` at kalde `Destroy` hvis vi ønsker at fjerne et objekt. I dette kald er det implicit at `Destroy`-metoden er en statisk metode på klassen `GameObject`. Det samme gælder med metoder på objekter, som vi altid skal kalde med `Metode`.

___
## Datatyper og variable
I C# kan typen ofte infereres så man ikke behøver være eksplicit omkring den, det gøres med `var`. Typen kan som udgangspunkt altid infereres hvis der assignes noget til den med `=`. Hvis ikke skal typen altid angives.

```csharp
var a = 5           // Implicit integer variabel
var b = 5.0f        // Implicit float variabel
var c = "John"      // Implicit string variabel

int a = 5           // Eksplicit integer variabel
float b = 5.0f      // Eksplicit float variabel
string c = "John"   // Eksplicit string variabel
```

### Editor variable
Hvis du vil erklære en variabel, som kan ændres i Unity's Editor skal den være __mutable__ og __serializable__:
```csharp
[SerializeField]
public float Variable = 5.0f
```
I nogle tilfælde vil du måske undgå at give en variabel en værdi med det samme. Så skal variablen erklæres på denne måde:
```csharp
[SerializeField]
public float Variable;
```
Dette er fordi F# skal kunne udregne variablens type. Typen skal angives da den ikke kan gætte typen uden.
___
## Erklæring af typer (klasser)
```csharp
using UnityEngine;

class MyType : MonoBehaviour 
{
    [SerializeField]
    public string Message = "Hello from C#";

    public void Start() 
    {
        Debug.Log("MonoBehaviour says: " + Message);
    }
}
```
Denne stump kode erklærer en `MonoBehaviour`, som har én instansvariabel `Message`, der bliver printet når Unity's Editor startes. `SerializeField` betyder at instansvariablen `Message` bliver tilgængelig i Unity's Editor.

<div class="note-box">
    For at kunne nedarve fra <code>MonoBehaviour</code> skal man <i>åbne</i> UnityEngine ( <code>open UnityEngine</code> ), ligesom man i C# skal <i>bruge</i> UnityEngine ( <code>using UnityEngine;</code> )
</div>

___
## Funktioner & metoder
I F# findes der både funktioner og metoder. Funktioner er ikke knyttet til nogen klasseinstans, hvilket betyder at du ikke kan tilgå `this` i funktioner (næsten ligesom `static` metoder i C#). Metoder er bundet på klasseinstanser og fungerer som vi kender det fra C#.

### Funktioner
Her erklærer vi en funktion, som tager en liste af tal og summerer dem efter de er blevet opløftet i `n`:
```fsharp
let sumInPowerN (nums:float32 list) (n:float32) =
    List.reduce (fun acc i -> acc + Mathf.Pow(i, n)) nums
```
Implicitte typer virker i nogle tilfælde også på funktioner, men compileren kan sommetider blive lidt forvirret og give nogle mystiske fejlbeskeder, så det kan ofte være en god idé at erklære deres typer eksplicit. Ovenstående funktion kunne også erklæres med:
```fsharp
let sumInPowerN nums n = [...]
```

### Currying
F# bruger currying ved funktionskald. Antag at vi har den følgende F# funktion, som tager en liste samt en funktion som bliver kørt på alle elementerne:
```fsharp
let rec executeOnElements list func =
    match list with
    | [] -> ()
    | h::t ->
        func h
        executeOnElements t func
```
Vi kan curry denne funktion og dermed lave en ny funktion, som udfører en handling på alle tal fra 1 til og med 100:
```fsharp
let doFrom1To100 = executeOnElements [1..100]
```
Dette betyder altså at hvis du kalder en funktion med for få argumenter vil den ikke give en fejl, som du måske kender det fra C#, men i stedet returnere en ny funktion som tager imod de resterende argumenter, og så giver det endelige resultat

Vores nye funktion kan nu anvendes således:
```fsharp
doFrom1To100 (fun i -> printfn "%d" i)

doFrom1To100 (fun i -> printfn "%d" (i * 5))
```
Hvilket udprinter alle tal fra 1 til 100 og derefter de første 100 tal i 5-tabellen.
_Læs mere om currying [her](https://fsharpforfunandprofit.com/posts/currying/)_

### Metoder
Metoder skal erklæres på en type og så kan man tilgå dens felter og properties:

```fsharp
type MoveForward() =
    inherit MonoBehaviour()

    [<SerializeField>]
    let mutable Speed = 8.0f

    member this.Update() =
        this.transform.position <- this.transform.position + (this.transform.forward * Speed * Time.deltaTime)
```

### Unity-specifikke metoder
Her giver vi nogle eksempler på Unity-specifikke metoder du måske kan få brug for.

#### Instantiate
Når man instantierer i F# Unity gøres det på samme måde som i C#.
```fsharp
    //Type 'GameObject' via casting
    let gObj = GameObject.Instantiate(prefab, this.transform.position, Quaternion.identity) :?> GameObject
```
```fsharp
    //Type 'GameObject' via generics
    let gameObject = GameObject.Instantiate<GameObject>(prefab, this.transform.position, Quaternion.identity)
```

#### Component referencer
Ligesom i C# er der to måder at få referencer til Components på: I editoren med `[<SerializeField>]` eller i koden med `GetComponent<T>`.

```fsharp
    //Skal sættes i editoren
    [<SerializeField>]
    let mutable myRigidbody:Rigidbody2D = null
```

```fsharp
    //Skal sættes i koden
    let mutable myRigidbody:Rigidbody2D = null

    member this.Start() =
        myRigidbody <- this.GetComponent<Rigidbody2D>()
```
<!--
#### MonoBehaviour Messages
Metoder som `Awake`, `Start` og `Update` og Kollision-metoder er alle kaldt Messages i Unity. Det samme gælder for f.eks collision metoder, derfor skal de alle defineres på samme måde. Du kan finde en liste af disse metoder på [Unitys dokumentation for MonoBehaviour](https://docs.unity3d.com/ScriptReference/MonoBehaviour.html). Her giver vi eksempler på `OnCollisionEnter2D` og `OnMouseDown`

```fsharp
member this.OnCollisionEnter2D(collision:Collision2D) =
    if collision.gameObject.CompareTag("Player") then
        Debug.Log "Ramte spiller, du tabte!"
    else
        let msg = sprintf "Ramte en %s" collision.gameObject.tag
        Debug.Log msg
```

```fsharp
member this.OnMouseDown() =
        SceneManager.LoadScene("Min scene")
```
-->
___
## Concurrency i C#
I C# eksisterer der flere forskellige parallelisme-mønstre. I dette dokument vil vi introducere _task-based asynchronous pattern_ som blev introduceret i .NET 4.0. Dette mønster gør brug af `Task` klassen som repræsenterer en lille opgave som skal eksekveres på en anden tråd end main tråden.

```csharp
await Task.Run(() => Console.WriteLine("Hello World!"));

Task<int> value = Task.Run(() => 1);
int value = await Task.Run(() => 1);
```
Her bliver `await` brugt til at vente på `Task`'en i stedet for at bruge `Task.Wait()`. `await` er et keyword som afventer returværdien af en `Task`. Keywordet kan kun bruges i en metode, som er markeret som `async`, som betyder at returværdien fra metoden altid er af typen `Task`.

## Concurrency på lister
Hvis man har en samling af data kan man behandle hvert element med en `Task`. I det følgende kode har vi en liste med spiller navne. For hver af disse spillere ønsker vi at hente deres highscore fra en server. Lad os antage at vi har en metode, `GetHighscoreAsync`, som tager en streng og returnere den tilhørende highscore. Med dette setup kan vi starte en `Task` for hver opgave i listen af spillere.
```csharp
var players = new List<string>() { "xXxkiller26xXx", "MurderHobo28", "fighter98" }
var highscores = Task.WhenAll(players.Select(p => GetHighscoreAsync(p)));
```
Listen `highscores` kommer til at indeholde alle spillernes highscores.

Dette er blot en af måderne hvorpå lister kan behandles asynkront. Der findes andre meetoder (f.eks. `IEnumerable.AsParallel` og `Parallel.ForEach`), som tjener lignende formål.

_Du kan også læse mere om `Task`s på [Microsofts Officielle Documentation](https://docs.microsoft.com/en-us/dotnet/api/system.threading.tasks.task?view=netstandard-2.0)_.


### Loops og ranges
Der findes også loops i F#. Disse er tætknyttede på ranges, så vi præsenterer begge samtidig:
```fsharp
let mutable sum = 0
for i in 1 .. 3 do
    sum <- sum + i
```
I dette eksempel er `1 .. 3` en range, som vil returnere listen bestående af tallene 1, 2 og 3. Selve syntaksen og den måde loopet fungerer på minder meget om `foreach`-loops fra C#, eller loops og ranges fra Python. Ranges kan også bruges til at deklarere lister:
```fsharp
let oneToHundred = [1..100]
```

<div class="note-box">
Bloggen <i>F# for Fun and Profit</i> anbefaler at undgå loops og i stedet bruge nogle af de andre List metoder, f.eks. List.iter.

<i>Læs mere om hvordan du bruger, og undgår at bruge, loops i F# <a href="https://fsharpforfunandprofit.com/posts/control-flow-expressions/#loops">her</a></i>
</div>

___

### Tuple Operatoren
Man skal være opmærksom på at når `*` operatoren bruges i deklarationer, betyder den ikke gange. I stedet bruges den som pardannelsesoperator, hvilket vil sige at højre og venstre siden af operatoren bliver sammen sat som en ny tuple.

```fsharp
let t1:int*float = (2, 3.14)
```
Ovenstående deklarerer en tuple med første element som integer og andet element som en float.
___
## Map-reduce
To vigtige koncepter i funktionel programmering, som vi har berørt en smule her, er map og reduce. Begge koncepterne behandler samlinger. **Map** transformerer alle elementerne i en liste og returnerer en ny samling og **reduce** reducerer alle elementerne i en samling til ét element.

Vi kan foreksempel bruge map til at udregne kvadratet af alle elementer af en liste:
```fsharp
let sqList = [1..5] |> List.map (fun i -> float32(i) ** 2.0f)
```
Og reduce til at summere alle elementerne i listen:
```fsharp
let sum = [1..10] |> List.reduce (fun acc elm -> acc + elm)
```
Eller som vi så tidligere:
```fsharp
let sum = [1..10] |> List.sum
```


___
## Events
Events fungere cirka på samme måde som i C#. Dog skal et event altid have et argument med. Så når man ikke er interesseret at give et argument med, benytter man enten `unit` eller wildcard `_` (så kan man ignorere typen).
```fsharp
    let event = new Event<_>()
    let Event = event.Publish
    
    let eventMedParameter = new Event<GameObject>()
    let EventMedParameter = eventMedParameter.Publish
```

Handlers skal tilføjes som en funktion. Hvis ikke din funktion bruger parametre (som `myEventHandler` herunder) skal den have unit `()` med uanset. 
Hvis du derimod er interesseret i at give en parameter med kan man benytte et lambda udtryk, se hvor `myParameterEventHandler` bliver tilføjet, efter `Debug.Log` i `Start`.
```fsharp
let handleEvent () =
        Debug.Log("Raised empty event!")

let handleEventMedParameter gameObject =
        Debug.Log(gameObject.name)   
       
member this.Start() =
    Event.AddHandler(Handler<_>(handleEvent))
    EventMedParameter.AddHandler(Handler<GameObject>(handleEventMedParameter))
```
For at raise eller trigger et event kaldes `.Trigger(...)` på eventet.
```fsharp
member this.Update() =
    if(Input.GetButtonDown("Jump")) then
        event.Trigger()                         //Ingen parameter (altså et event<unit>
        eventMedParameter.Trigger(somePrefab)   //Parameter med typen GameObject
```
Alternativt kan du bruge lambda-funktioner til at tilføje event-handlers:
```fsharp
member this.Start() =
    Event.AddHandler(Handler<_>(fun _ e -> Debug.Log("event triggered")))
    EventMedParameter.AddHandler(Handler<GameObject>(fun _ g -> Debug.Log(g.name)))
```
___

## Concurrency

Concurrency er et rimeligt omfattende emne og vi har derfor lavet separate filer.

[Læs om concurrency i C#](csharp-concurrency.md)

[Læs om concurrency i F#](fsharp-concurrency.md)
