# Concurrency i C#
I C# bruger eksisterer der flere forskellige parallelisme-mønstre. I dette dokument vil vi introducere _task-based asynchronous pattern_ som blev introduceret i .NET 4.0. Dette mønster gør brug af `Task` klassen som repræsentere en lille opgave som skal eksekveres på en anden tråd end main tråden.

## Tasks
Der er to varianter af denne klasse, en med returværdi og en uden. `Task`s er tiltænkt at skulle køres asynkront, men kan også eksekveres synkront hvis nødvendigt.
```csharp
Task noReturn = new Task(someAction);

Task<int> intReturn = new Task<int>(someAction);
```
I den ovenstående eksempel er `someAction` en funktion, metode eller et lambda udtryk som `Task`'en udfører. Efter at en `Task` er blevet oprettet kan den køres ved brug af `.Start()` metoden. Hvis main tråden skal vente (synkront) på at en `Task` bliver færdig kan `.Wait()` metoden køres.
```csharp
Task print = new Task(() => Console.WriteLine("Hello World!");)

print.Start();
print.Wait();
```


Alternativt kan man gøre brug af `.Run` metoden til at lave og køre en `Task` med det samme. Nedenunder kan ovenstående eksempel skrevet om til at bruge `.Run`.
```csharp
await Task.Run(() => Console.WriteLine("Hello World!"););
```
Man kan give argumenter til en `Task` på følgende måde.
```csharp
await Task.Run((str) => Console.WriteLine(str););
```

[Læs mere om Tasks](https://docs.microsoft.com/en-us/dotnet/api/system.threading.tasks.task?view=netstandard-2.0)
