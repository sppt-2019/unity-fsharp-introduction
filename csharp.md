## Generel information om C# & Unity
C# er et objekt orienteret programmeringssprog som kører i .NET Platformen. Eftersom C# er Objekt orienteret betyder det at næsten alt betragtes som Objekter og funktionskald. Syntaksen i C# er inspireret af Smalltalk og C++ og er curly braces ( `{` og `}` ) baseret, istedet for indentations-baseret som vi kender det fra F#.

I C# er der er implicit membership. I C# vil det være typisk i et `GameObject` at kalde `Destroy` hvis vi ønsker at fjerne et objekt. I dette kald er det implicit at `Destroy`-metoden er en statisk metode på klassen `GameObject`. Det samme gælder med metoder på objekter, som vi altid skal kalde med en `Metode`.

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
Hvis du vil erklære en variabel, som kan ændres i Unity's Editor skal den være __serializable__:
```csharp
[SerializeField]
public float Variable = 5.0f
```
I nogle tilfælde vil du måske undgå at give en variabel en værdi med det samme. Så skal variablen erklæres på denne måde:
```csharp
[SerializeField]
public float Variable;
```
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
    For at kunne nedarve fra <code>MonoBehaviour</code> skal man <i>bruge</i> ( <code>using UnityEngine;</code> )
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
Instantiering i C# Unity gøres det på denne måde. 
```csharp
    //Type 'GameObject' via casting
    gObj = GameObject.Instantiate(prefab, transform.position, Quaternion.identity) as GameObject
```
```csharp
    //Type 'GameObject' via generics
    gameObject = GameObject.Instantiate<GameObject>(prefab, transform.position, Quaternion.identity)
```

#### Component referencer
Ligesom i C# er der to måder at få referencer til Components på: I editoren med `[<SerializeField>]` eller i koden med `GetComponent<T>`.

```csharp
    //Sættes i editoren
    [SerializeField]
    public Rigidbody2D MyRigidbody;
```

```csharp
    //Sættes med kode
    public Rigidbody2D MyRigidbody;

    
    public void Start() 
    {
        MyRigidbody = GetComponent<Rigidbody2D>()
    }
```
<!--
#### MonoBehaviour Messages
Metoder som `Awake`, `Start` og `Update` og Kollision-metoder er alle kaldt Messages i Unity. Det samme gælder for f.eks collision metoder, derfor skal de alle defineres på samme måde. Du kan finde en liste af disse metoder på [Unitys dokumentation for MonoBehaviour](https://docs.unity3d.com/ScriptReference/MonoBehaviour.html). Her giver vi eksempler på `OnCollisionEnter2D` og `OnMouseDown`

```csharp
void OnCollisionEnter2D(Collision2D collision) =
    if (collision.gameObject.CompareTag("Player"))
    {
        Debug.Log("Ramte spiller, du tabte!");
    }
    else
    {
        Debug.Log($"Ramte en {collision.gameObject.tag}");
    }
```

```csharp
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
Der findes også loops i C#.
```csharp
var sum = 0;
for(int i = 0; i < 3; i++)
{
    sum += i;
}
```
I C# har vi også foreach løkker, til at itterer over en collection.

```csharp
var sum = 0;
foreach (int element in Enumerable.Range(0, 3))
{
    sum += element;
}
```
___

### Tuple Operatoren
I C# for at lave pardannelse tuple af flere typer skal man deklarere typerne med variablen først, efterfuglt af selve værdien på hver enkel variable i tuplen. Dette bliver så sat sammen til en ny tuple.

```csharp
// Ny syntaks for tuples
var personTuple = (23, 1.77f); // Implicitte typer
var (int, float) personTuple = (23, 1.77f); // Eksplicitte typer

// Gammel syntaks for tuples
var = new Tuple<int, float>(23, 1.77f); // Implicitte typer
Tuple<int, float> personTuple = new Tuple<int, float>(23, 1.77f); // Eksplicitte typer


// Tuples kan udpakkes så de er lettere at bruge
(int age, float height) = (23, 1.77f);
```
Ovenstående deklarerer en tuple med første element som integer og andet element som en float.
___
## LINQ
I C# er behandling af samlinger af data meget let og elegant med LINQ

```csharp
class Person
{
    public string Name;
    public int Age;
    public string Country;
    
    public Person(string name, int age, string country)
    {
        Name = name;
        Age = age;
        Country = country;
    }
}

var persons = new Person[] 
{
    new Person("Daniel", 23, "The Netherlands"),
    new Person("Malte", 25, "Norway"),
    new Person("Charlie", 19, "Germany"),
    new Person("Thomas", 25, "Norway"),
    new Person("Balder", 35, "Valhal"),
    new Person("Magnus", 18, "The Netherlands"),
    new Person("Thor", 23, "Valhal"),
    new Person("Odin", 90, "Valhal"),
};

var asgardians = persons
                    .Where(person => person.Country == "Valhal")
                    .OrderBy(person => person.Name)
                    .ThenBy(person => person.Age);
                    
var mayDrinkInTheUS = persons
                        .Where(person => person.Age >= 21)
                        .OrderBy(person => person.Country);
                    
var averageDutchAge = persons
                        .Where(person => person.Country == "The Netherlands")
                        .Average(person => person.Age);
                        
var namesOnly = persons
                    .Select(person => person.Name);
                    
var percentageScadinavians = (persons
                                .Where(person => person.Country == "Norway" || person.Country == "Valhal")
                                .Count() / persons.Length) * 100

___
## Events
For at erklære et event i en given klasse skal man have to ting. Først skal eventet navngives og derudover skal man huske at `Invoke` eventet in en metode
```csharp
public event EventHandler StuffHappened;

private void ProcessStuff()
{
    // ...
    var blah = DoStuff();
    if (blah.HasStartedHappening)
    {
        StuffHappened?.Invoke(this, EventArgs.Empty);
    }
}
```

Handlers skal tilføjes som en funktion.
Hvis du derimod er interesseret i at give en parameter med kan man benytte et lambda udtryk, se hvor `myParameterEventHandler` bliver tilføjet, efter `Debug.Log` i `Start`.
```csharp
// Man kan subscribe til et event med et lambda-udtryk, en anonym metode eller en delegate:
StuffHappened += (s, e) => Debug.Log("Stuff just happened!");

// Men hvis man også vil kunne unsubscribe, skal man bruge en navngiven handler funktion:
public void OnStuffHappened(object sender, EventArgs args) => Debug.Log("Stuff just happened!");

StuffHappened += OnStuffHappened; // subscribe til event
StuffHappened -= OnStuffHappened; // unsubscribe fra event
```

I en Unity kontekst:
```csharp
public event EventHandler JumpPressed;
public event EventHandler FirePressed;

void Update()
{
    if (Input.GetButtonDown("Jump"))
        JumpPressed?.Invoke(this, EventArgs.Empty);
        
    if (Input.GetButtonDown("Fire1"))
        FirePressed?.Invoke(this, EventArgs.Empty);
}
```
___

## Concurrency

Concurrency er et rimeligt omfattende emne og vi har derfor lavet separate filer.

[Læs om concurrency i C#](csharp-concurrency.md)

[Læs om concurrency i F#](fsharp-concurrency.md)
