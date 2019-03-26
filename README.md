# Samplesheet

## Generel information om F#
F# er et funktionelt programmeringssprog som kører i .NET Platformen sammen med C#. Eftersom F# er funktionelt betyder det at næsten alt betragtes som funktioner og funktionskald. F# er desuden _pure_ som standard, hvilket betyder at variable ikke må ændres efter de er blevet erklæret. Syntaksen i F# er inspireret af Haskell og er indentations-baseret.

En ting der er vigtig at være opmærksom på i F# i forhold til C# er at der er eksplicit membership. I C# vil det være typisk i et `GameObject` at kalde `Destroy` hvis vi ønsker at fjerne et objekt. I dette kald er det implicit at `Destroy`-metoden er en statisk metode på klassen `GameObject`. I F# må vi være eksplicitte og derfor kalde `GameObject.Destroy`. Det samme gælder med metoder på klasser, som vi altid skal kalde med `this.Metode`.

Dette dokument giver en hurtig introduktion til F# og hvordan det kan bruges i Unity.

___
## Datatyper og variable
Alle datatyper som du kender fra C# kan også bruges i F#. Variabelerklæring i F# er næsten det samme som i C#, den eneste forskel er at vi bruger `let` i stedet:

```fsharp
let a = 5             //Integer variabel
let mutable b = 5.0f  //Overskrivbar float32 variabel
let c = "John"        //String variabel
let d:bool = true     //Eksplicit typet boolsk variabel
```

### Editor variable
Hvis du vil erklære en variabel, som kan ændres i Unity's Editor skal den være __mutable__ og __serializable__:
```fsharp
[<SerializeField>]
let mutable Variable = 5.0f
```
I nogle tilfælde vil du måske undgå en default værdi. Så skal variablen erklæres på denne måde:
```fsharp
[<DefaultValue>]
val mutable Variable:float32
```
Dette er fordi F# skal kunne udregne variablens type.
___
## Type casting
F# er et stærkt typet sprog og det vil ofte være nødvendigt at caste variable. Et type cast fra `int` til `float32`:
```fsharp
let i = 14
let f = float32 i
```

___
## Erklæring af typer (klasser)
```fsharp
type TypeName() =
    inherit UnityEngine.MonoBehaviour()

    [<SerializeField>]
    let mutable Message = "Hello from F#"

    member this.Start() =
        Debug.Log("MonoBehaviour says: " + Message)
```
Denne stump kode erklærer en `MonoBehaviour`, som har én instansvariabel `Message`, der bliver printet når Unity's Editor startes. `SerializeField` betyder at instansvariablen `Message` bliver tilgængelig i Unity's Editor.
___
## Funktioner & metoder
I F# findes der både funktioner og metoder. Funktioner er ikke knyttet til nogen klasseinstans, hvilket betyder at du ikke kan tilgå `this` i funktioner (næsten ligesom `static` metoder i C#). Metoder er bundet på klasseinstanser og fungerer som du kender det fra C#.

### Funktioner
Her erklærer vi en funktion, som tager en liste af tal og summerer dem efter de er blevet opløftet i `n`:
```fsharp
let sumInPowerN (nums:float32 list) (n:float32) =
    List.reduce (fun acc i -> acc + Mathf.Pow(i, n)) nums
```
Implicitte typer virker i nogle tilfælde også på funktioner, men compileren kan sommetider blive lidt forvirret og give nogle mystiske fejlbeskeder, så det oftest er bedst at erklære deres typer eksplicit. Ovenstående funktion kunne også erklæres med:
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
Vi kan curry'e denne funktion og dermed lave en ny funktion, som udfører en handling på alle tal mellem 1 og 100:
```fsharp
let doFrom1To100 = executeOnElements [1..100]
```
Dette betyder altså at hvis du kalder en funktion med for få argumenter vil den ikke give en fejl, som du måske kender det fra C#.
Vores nye funktion kan nu anvendes således:
```fsharp
doFrom1To100 (fun i -> printfn "%d" i)
```
Hvilket udprinter alle tal fra 1 til 100.

### Metoder
Metoder skal erklæres på en type og kan tilgå dens felter og properties:

```fsharp
type MoveForward() =
    inherit MonoBehaviour()

    [<SerializeField>]
    let mutable Speed = 8.0f

    member this.Update() =
        this.transform.position <- this.transform.position + (this.transform.forward * Speed * Time.deltaTime)
```

___
## Kontrolstrukturer

### If-else
If-else kontrolstrukturer findes i F#:
```fsharp
type Foods =
    | Strawberry
    | IceCream
    | Sandwiches
    | Pizza

let GetFoodMessage food =
    if food = Strawberry then
        "I see you like fruit"
    elif food = IceCream then
        "So you have a sweet tooth? Watch you weight!"
    else
        "There are so many options when it comes to food."
```
I de fleste tilfælde vil vi dog bruge pattern maching, da det er smartere:

### Pattern Matching
Pattern matching kan beskrives som if-else statements på steroider. Du kan bruge dem til både at matche på variable, tuples, klasser osv.

#### Simpel pattern matching
```fsharp
let PrintFoodMessage food =
    match food with
    | Strawberry -> Debug.Log("I see you like fruit")
    | IceCream -> Debug.Log("So you have a sweet tooth? Watch you weight!")
    | _ -> Debug.Log("There are so many options when it comes to food.")
```
Dette er et simpelt eksempel, som er ækvivalent med ìf-else eksempelet ovenover.

Forestil dig nu at vi har tuples af mad og antallet af den type mad en person har spist hver dag:

#### Pattern matching på lister
```fsharp
let diet = [(IceCream,20);(Sandwiches,0)]
match diet with
| [(IceCream,0);(Sandwiches,x)] when x > 0 -> "Healthy diet with no ice cream and sandwiches"
| [(IceCream,y);(Sandwiches,0)] when y > 0 -> "More sandwiches and less ice cream!"
| [(Pizza,z)] when z > 0 -> "I hope that pizza was made from whole-grain flour!"
| _ -> "Nothing special to notice about your diet"
```

Alternativt kan listen også behandles som `head` og `tail` gennem recursion:
```fsharp
let rec GetFoodMessage diet =
    match list with
    | [] -> ""
    | (IceCream,x)::t when x > 0  -> "Less IceCream" + (GetFoodMessage t)
    | (Sandwiches,0)::t -> "More Sandwiches" + (GetFoodMessage t)
    | (f, q) -> (sprintf "You diet of %d %ss is fine" q f.ToStrings) + (GetFoodMessage t)
```

#### Pattern matching på typer
Pattern matching kan også matche på typer, hvilket du sandsynligvis vil få brug for i opgaverne. Dette eksempel viser hvordan:

```fsharp
type Weather =
| Snowing of cmOfSnow:int * temp:float32
| Sunny of temp:float32
| Storm of windSpeed:int * temp:float32

let weaterAnnouncement w =
    match w with
    | Snowing (s,t) -> sprintf "%d cm of snow has fallen and it's %f degrees outside" s t
    | Sunny (t) -> "Sun's high in the sky and it's " + t.ToString() + " degrees outside"
    | Storm (w,t) -> sprintf "Stay inside, as winds are reaching %d m/s with a temperature of %f" w f
```

Pattern matching er et meget kraftfuldt værktøj, men også lidt for omfattende til at gennemgå på dette ark. Mere hjælp kan findes på https://fsharpforfunandprofit.com/posts/match-expression/.


### Loops og ranges
Der findes også loops i F#. Disse er tætknyttede på ranges, så vi præsenterer begge samtidig:
```fsharp
let mutable sum = 0
for i in 1 .. 3 do
    sum <- sum + i
```
I dette eksempel er `1 .. 3` en range, som vil summere listen bestående af tallene 1, 2 og 3. Selve syntaksen og den måde loopet fungerer på minder meget om `foreach`-loops fra C#, eller loops og ranges fra Python. Ranges kan også bruges til at deklarere lister:
```fsharp
let oneToHundred = [1..100]
```

<div class="note-box">
Bloggen <i>F# for Fun and Profit</i> anbefaler at undgå loops og i stedet bruge nogle af de andre List metoder, f.eks. List.iter.
</div>

___
## F# Operatorer
De fleste operatorer som du kender fra C# findes også i F#. Der er dog nogle få undtagelser som vi gennemgår her:

### Assignment vs. boolean expressions
I C# er du nok vandt til at bruge `=` som assignment og `==` som boolsk sammenligning. I F# er tingene lidt anderledes, her bruges `=` til deklarationer med `let` operatoren og `<-` som assignment når vi ønsker at overskrive en variabel (virker kun på `mutable` variable). I boolske udtryk bruger vi `=` som boolsk lighed:
```fsharp
let mutable i = 10
if i = 8 then
    i <- 0
else
    i <- i + 1
```

<div class="note-box">
Bloggen <i>F# for Fun and Profit</i> anbefaler at undgå mutable variable i F# så vidt som muligt.
</div>

<div class="note-box">
Compound operatorer (+=, *= osv.) findes ikke i F#, da vi som udgangspunkt ikke overskriver variable.
</div>

### Pipe operatoren
I et tidligere eksempel så vi pipe operatoren (`|>`) i brug.

Denne operator er især smart når vi arbejder med samlinger af objekter eller værdier. Kort fortalt tager den værdien på venstre hånd og bruger som sidste argument i funktionen på højre hånd. I dette eksempel finder vi alle `GameObject`s med tagget `Movable` og beregner deres midtpunkt:
```fsharp
[1..5]
|> List.map (fun i -> float32(i))
|> List.map (fun f -> f ** 2.0f)
|> List.reduce (fun acc elm -> acc + elm)
```
Følgende er en forklaring af hver skridt:

1) `[1..5]` er en range som erklærer en liste af integers fra og med 1 til og med 5.
2) `List.map (fun i -> float32(i))` transformerer listen af integers til en liste af floats.
3) `List.map (fun f -> f ** 2.0f)` opløfter alle elementerne i listen i anden potens.
4) `List.reduce (fun acc elm -> acc + elm)` summerer alle tallene i listen.

Der findes også en operator til at pipe baglæns (`<|`), men den burde ikke blive nødvendig i denne opgave.
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
## FRP Event Handling
I Functional Reactive Programming (FRP) implementerer vi logikken for vores spil som en række event handlers. I pure funktionel programmering findes der ikke loops (det gør der i F# fordi det ikke er pure). Alt der skal gentages implementeres med rekursion. Af denne årsag findes `Update`-metoden som du kender fra C# Unity ikke i FRP-biblioteket. Du bliver nødt til at tænke `Update`-logikken ind i andre event handlers.

#### Registrer en event handler til Space-tasten
```fsharp
type Jumper() =
    inherit FRPBehaviour()

    member this.Start() =
        this.ReactTo (
            FRPEvent.Keyboard,                            //Event type
            (fun () -> Input.GetKeyDown(KeyCode.Space)),  //Filtrering
            (fun () -> HandleSpaceEvent()))               //Handler
```
En FRP-registrering består af tre ting:

1) Event typen, som i dette tilfælde er en af værdierne af enum'en `FRPEvent`.
2) En filtreringsfunktion, som sorterer i hvilke events vi ønsker at reagere på. I ovenstående tilfælde er det funktionen `Input.GetKeyDown(KeyCode.Space)`.
3) En handler, som implementerer hvad der skal ske når dette event forekommer.

#### Registrer en event handler til kollisioner
```fsharp
type Player() =
    inherit FRPBehaviour()

    [<SerializeField>]
    let mutable Health = 10

    member this.TakeDamage() =
        Health <- Health - 1

    member this.Start() =
        this.ReactTo<Collider> (
            FRPEvent.TriggerEnter,
            (fun c -> not (c.GetComponent<Shot>() = null)),
            (fun c ->
                this.TakeDamage()
                GameObject.Destroy(c.gameObject)))
```
Når man registrerer event handlers til kollisioner skal man bruge den generiske version af `this.ReactTo`. Dette er for at fortælle hvad for en type klasse vi forventer at reagere på i condition'en og handleren. I dette eksempel vil `c` være den `Collider`, som `Player` kolliderede med.

### Registrer en event handler til musen
```fsharp
type Mouse() =
    inherit FRPBehaviour()

    member this.Start() =
        this.ReactTo<float32*float32> (
            FRPEvent.MouseMove,
            (fun m ->
                let (h, v) = m.Deconstruct()
                let mSpeed = Mathf.Sqrt(h ** 2.0f + v ** 2.0f)
                let dir = new Vector3(h, 0.0f, v) |> Vector3.Normalize
                this.GetComponent<Rigidbody>().AddForce(dir * mSpeed)
            )
        )
```
`MouseMove` eventet har den generiske type `float32*float32`, hvilket betyder en tuple bestående af to floats. Disse to floats er respektivt bevægelsen på X-aksen og Y-aksen. Det samme er tilfældet med `MoveAxis`-eventet.

En anden ting der er værd at bemærke her er at vi ikke har nogen filtreringsfunktion. Det betyder at handleren vil blive kaldt hver gang musen bevæger sig.

### Forskellige typer events
Dette er en liste over events samt deres Unity C# modsvar og deres generiske type:

| Event Type | Unity Modsvar | Generisk type |
| ---------- | ------------- | ------------- |
| Update | Update | `_` |
| MoveAxis | `Input.GetAxis("Horizontal/Vertical")` | `float32*float32` |
| Keyboard | `Input.GetKeyDown()` | `_` |
| MouseMove| `Input.GetAxis("Mouse X/Y")` | `float32*float32` |
| MouseClick | `Input.GetMouseButton` | `_` |
| CollisionEnter | OnCollisionEnter | `Collision` |
| CollisionExit | OnCollisionExit | `Collision` |
| TriggerEnter | OnTriggerEnter | `Collider` |
| TriggerExit | OnTriggerExit | `Collider` |

Hvis man forsøger at bruge en generisk type, som ikke passer til event typen vil der blive kastet en exception på runtime.
