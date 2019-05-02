## General information on C# & Unity 
C# is a object oriented programming language that runs in the .NET Platform. Since C# is a object oriented it means that almost everything is considered as objects and function calls. The syntax in C# is inspired by Smalltalk and C++ and is a curly braces ( `{` and `}` ) based, instead of indention-based like we know from F#.

In C# there is implicit membership. In C# it would be typical for a `GameObject` to call `Destroy` if we wished to remove a object. In this call it is implicit that the `Destroy`-method is a static method on the class `GameObject`. It is the same case with methods on objects, we will always need to call them with a `Method`.

___
## Datatypes og variables
In C# types can often be inferenced such that there is no need to be explicit about it, this is done with `var`. The type can as a starting point always be inferenced if it is assigned to something with `=`. If not the type always need to be stated.

```csharp
var a = 5;           // Implicit integer variable
var b = 5.0f;        // Implicit float variable
var c = "John";      // Implicit string variable

int a = 5;           // Eksplicit integer variable
float b = 5.0f;      // Eksplicit float variable
string c = "John";   // Eksplicit string variable
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
This piece of code decalres a `MonoBehaviour`, that has a instance variable `Message`, such that it is printed when Unity's Editor starts. `SerializeField` means that the instance variable `Message` becomes availible in Unity's Editor.

<div class="note-box">
    To be able to inherit <code>MonoBehaviour</code> you have to <i>use</i> ( <code>using UnityEngine;</code> )
</div>

___
## Functions & Methods
In C# there exist both function and methods. It is often the methods that are called as functions in C#, but it is not always the case. In this test it is only necessary to use methods.
Functions are bound to any class instance, which means that you can't access `this` in functions (almost like `static` methods). Methods are bound to objects, and works as you most likely know in C#.

### Methods
Methods need to be declared in a class. Methods can access classes field properties. Here is both `Update` and `Move` methods:
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

### Functions
Functions are a rare use in C#, but there is a difference between them and methods.
<!-- Here we declare a function that takes a list of number and sums them after they have been lifted in `n`: -->
```csharp
Func<int, int> multiplyByFive = num => num * 5;
// Returns 35
int result = multiplyByFive(7);
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
    //Done in editor
    [SerializeField]
    public Rigidbody2D MyRigidbody;
```

```csharp
    //Done in code
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
// New syntax for tuples
var personTuple = (23, 1.77f);          // Implicit type
(int, float) personTuple = (23, 1.77f); // Explicit type

// Old syntax for tuples
var personTuple = new Tuple<int, float>(23, 1.77f); // Implicit type
Tuple<int, float> personTuple = new Tuple<int, float>(23, 1.77f); // Explicit type


// Tuples can be unpacked such as they are easier to use
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
// You can subscribe to events with a lambda-expression, a anonymous method or a delegate:
StuffHappened += (s, e) => Debug.Log("Stuff just happened!");

// But if you also want to unsubscribe, you need to use a namegiven handler function:
public void OnStuffHappened(object sender, EventArgs args) => Debug.Log("Stuff just happened!");

StuffHappened += OnStuffHappened; // subscribe to event
StuffHappened -= OnStuffHappened; // unsubscribe from event
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
