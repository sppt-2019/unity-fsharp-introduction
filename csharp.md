## Generel information om C# & Unity
C# er et objekt orienteret programmeringssprog som kører i .NET Platformen. Eftersom C# er Objekt orienteret betyder det at næsten alt betragtes som Objekter og funktionskald. Syntaksen i C# er inspireret af Smalltalk og C++ og er curly braces ( `{` og `}` ) baseret, istedet for indentations-baseret som vi kender det fra F#.

I C# er der er implicit membership. I C# vil det være typisk i et `GameObject` at kalde `Destroy` hvis vi ønsker at fjerne et objekt. I dette kald er det implicit at `Destroy`-metoden er en statisk metode på klassen `GameObject`. Det samme gælder med metoder på objekter, som vi altid skal kalde med en `Metode`.

___
## Datatyper og variable
I C# kan typen ofte infereres så man ikke behøver være eksplicit omkring den, det gøres med `var`. Typen kan som udgangspunkt altid infereres hvis der assignes noget til den med `=`. Hvis ikke skal typen altid angives.

```csharp
var a = 5;           // Implicit integer variabel
var b = 5.0f;        // Implicit float variabel
var c = "John";      // Implicit string variabel

int a = 5;           // Eksplicit integer variabel
float b = 5.0f;      // Eksplicit float variabel
string c = "John";   // Eksplicit string variabel
```

### Editor variable
Hvis du vil erklære en variabel, som kan ændres i Unity's Editor skal den være __serializable__:
```csharp
[SerializeField]
public float Variable = 5.0f;
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
I C# findes der både funktioner og metoder. Ofte bliver metoder også kaldt for funktioner i C#, men dette er ikke altid tilfældet. I denne test er det kun nødvendigt at benytte metoder.
Funktioner er ikke knyttet til nogen klasseinstans, hvilket betyder at du ikke kan tilgå `this` i funktioner (næsten ligesom `static` metoder). Metoder er bundet på objekter, og fungerer som nok som du kender det fra C#.

### Metoder
Metoder skal erklæres i en klasse. Metoder kan tilgå klassens felter properties. Her er både `Update` og `Move` metoder:

```csharp
class MoveForward : MonoBehaviour
{
    [SerializeField]
    float Speed = 8.0f

    void Update()
    {
        transform.position = Move(Speed);
    }
    
    private Vector3 Move(float addSpeed)
    {
        return transform.position + (transform.forward * addSpeed * Time.deltaTime);
    }
        
}
```

### Funktioner
Funktioner er sjældent brugt i C#, men der er forskel på dem og metoder.
<!-- Her erklærer vi en funktion, som tager en liste af tal og summerer dem efter de er blevet opløftet i `n`: -->
```csharp
Func<int, int> multiplyByFive = num => num * 5;
// Returns 35
int result = multiplyByFive(7);
```



### Unity-specifikke metoder
Her giver vi nogle eksempler på Unity-specifikke metoder du måske kan få brug for.

#### Instantiate
Instantiering i C# Unity gøres det på denne måde. 
```csharp
//Type 'GameObject' via casting
var gObj = Instantiate(prefab, transform.position, Quaternion.identity) as GameObject;
```
```csharp
//Type 'GameObject' via generics
var gameObject = Instantiate<GameObject>(prefab, transform.position, Quaternion.identity);
```

#### Component referencer
Der er to måder at få referencer til Components på: I editoren med `public/[SerializeField]` eller i koden med `GetComponent<T>`.

```csharp
//Sættes i editoren
public Rigidbody2D MyRigidbody;

//Sættes også i editoren
 [SerializeField]
private BoxCollider2D colliderFromEditor;
```

```csharp
//Sættes med kode
private Rigidbody2D MyRigidbody;

public void Start() 
{
    MyRigidbody = GetComponent<Rigidbody2D>();
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

### Loops og ranges
Der findes også loops i C#.
```csharp
var sum = 0;
for(int i = 0; i < 3; i++)
{
    sum += i;
}
```
I C# har vi også foreach løkker, til at iterere over en collection. Denne snippet kræver: `using System.Linq;` i toppen af C# filen.

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
var personTuple = (23, 1.77f);          // Implicitte typer
(int, float) personTuple = (23, 1.77f); // Eksplicitte typer

// Gammel syntaks for tuples
var personTuple = new Tuple<int, float>(23, 1.77f); // Implicitte typer
Tuple<int, float> personTuple = new Tuple<int, float>(23, 1.77f); // Eksplicitte typer


// Tuples kan udpakkes så de er lettere at bruge
(int age, float height) = personTuple;
(var age, var height) = personTuple;
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
                                .Count() / persons.Length) * 100;
```

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
