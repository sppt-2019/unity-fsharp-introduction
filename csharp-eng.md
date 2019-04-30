## General information on C# & Unity 
C# is a object orientetet programming language that runs in the .NET Platform. Since C# is a objekt orientet it means that almost everything is considered as objects and functioncalls. The syntax in C# is inspired by Smalltalk and C++ and is a curly braces ( `{` og `}` ) based, instead of indention-based like we know from F#.

In C# there is implicit membership. In C# it would be typical for a `GameObject` to call `Destroy` if we wished to remove a object. In this call it is implicit that the `Destroy`-method is a static method on the class `GameObject`. It is the same case with methods on objects, we will always need to call them with a `Metode`.

___
## Datatypes og variables
In C# types can often be inferenced such that there is no need to be explicit about it, this is done with `var`. The type can as a starting point always be inferenced if it is assigned to something with `=`. If not the type always need to be stated.

```csharp
var a = 5;           // Implicit integer variabel
var b = 5.0f;        // Implicit float variabel
var c = "John";      // Implicit string variabel

int a = 5;           // Eksplicit integer variabel
float b = 5.0f;      // Eksplicit float variabel
string c = "John";   // Eksplicit string variabel
```

### Editor variables
If you want to declare a variable, that can be changed in Unity's Editor it needs to be __serializable__:
```csharp
[SerializeField]
public float Variable = 5.0f;
```
In some instances you might want to avoid giving a variable a value right away. Then the variable need to be declared this way:
```csharp
[SerializeField]
public float Variable;
```
___
## Declaring of types (class)
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
This peice of code decalres a `MonoBehaviour`, that has a instancevariabel `Message`, such that it is printed when Unity's Editor starts. `SerializeField` means that the instancevariable `Message` becomes availible in Unity's Editor.

<div class="note-box">
    For at kunne nedarve fra <code>MonoBehaviour</code> skal man <i>bruge</i> ( <code>using UnityEngine;</code> )
</div>

___
## Functions & Methods
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
class MoveForward : MonoBehaviour
{

    [SerializeField]
    let mutable Speed = 8.0f

    member this.Update() =
        this.transform.position <- this.transform.position + (this.transform.forward * Speed * Time.deltaTime)
}
```

### Unity-specific methods
Here we gives some examples of Unity-specific methods you might get a need to use.

#### Instantiate
Instanciating in C# Unity is done this way.
```csharp
    //Type 'GameObject' via casting
    gObj = GameObject.Instantiate(prefab, transform.position, Quaternion.identity) as GameObject;
```
```csharp
    //Type 'GameObject' via generics
    gameObject = GameObject.Instantiate<GameObject>(prefab, transform.position, Quaternion.identity);
```

#### Component reference
Like in C# there is two ways to reference to Components: In the editor with `[<SerializeField>]` or in the code with `GetComponent<T>`.

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

### Loops og ranges
There exist loops in C#.
```csharp
var sum = 0;
for(int i = 0; i < 3; i++)
{
    sum += i;
}
```
We also have foreach loops in C#, to be able to iterate over a collection.

```csharp
var sum = 0;
foreach (int element in Enumerable.Range(0, 3))
{
    sum += element;
}
```
___

### Tuple Operatoren
In C# to be able to make pairing tuple of multiple types, you have to declare the types with variables first, followed by the value on each variable in the tuple. 

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
The code above declares a tuple where the first element is a integer and the second is a float.
___
## LINQ
In C# treatment of a collection of data, is very easy and elegant with LINQ.

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
To be able to declare a event in a given class you have to make two things. First the event needs a name and then you need to remember to `Invoke` the event in a method.
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
Handlers need to be added as functions.
If you are interested in provide a parameter with which to use in a lambda expression, see where `myParameterEventHandler` gets added, after `Debug.Log` in `Start`.

```csharp
// Man kan subscribe til et event med et lambda-udtryk, en anonym metode eller en delegate:
StuffHappened += (s, e) => Debug.Log("Stuff just happened!");

// Men hvis man også vil kunne unsubscribe, skal man bruge en navngiven handler funktion:
public void OnStuffHappened(object sender, EventArgs args) => Debug.Log("Stuff just happened!");

StuffHappened += OnStuffHappened; // subscribe til event
StuffHappened -= OnStuffHappened; // unsubscribe fra event
```

In a Unity context:
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

Concurrency is a reasonably comprehensive topic so we have covered it in a separate file.

[Read about concurrency in C#](csharp-concurrency.md)

[Read about concurrency in F#](fsharp-concurrency.md)
