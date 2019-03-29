# Concurrency i C#
I C# eksisterer der flere forskellige parallelisme-mønstre. I dette dokument vil vi introducere _task-based asynchronous pattern_ som blev introduceret i .NET 4.0. Dette mønster gør brug af `Task` klassen som repræsenterer en lille opgave som skal eksekveres på en anden tråd end main tråden.

## Tasks
`Task`s er tiltænkt at skulle køres asynkront, men kan også eksekveres synkront hvis nødvendigt. Der er to varianter af denne klasse; en med returværdi og en uden:
```csharp
Task noReturn = new Task(someAction);

Task<int> intReturn = new Task<int>(someAction);
```
I den ovenstående eksempel er `someAction` en funktion, metode eller et lambda udtryk som `Task`'en udfører. Efter at en `Task` er blevet oprettet kan den køres ved brug af `.Start()` metoden. Hvis main tråden skal vente på at en `Task` bliver færdig kan `.Wait()` metoden køres.
```csharp
Task print = new Task(() => Console.WriteLine("Hello World!");)

print.Start();

Console.WriteLine("This code runs concurrently with the print task.");

print.Wait();
```

## Best Practice
Alternativt kan man gøre brug af `Task.Run` metoden til at lave og køre en `Task` med det samme:
```csharp
await Task.Run(() => Console.WriteLine("Hello World!"););
```
Her bliver `await` brugt til at vente på `Task`'en i stedet for at bruge `Task.Wait()`. `await` er et keyword som afventer returværdien af en `Task`. Keywordet kan kun bruges i en metode, som er markeret som `async`, som betyder at returværdien fra metoden altid er af typen `Task`.

## Concurrent Liste Operationer
Hvis man har en samling af data kan man behandle hvert element med en `Task`. I det følgende kode har vi en liste med spiller navne. For hver af disse spillere ønsker vi at hente deres highscore fra en server. Lad os antage at vi har en metode, `GetHighscoreAsync`, som tager en streng og returnere den tilhørende highscore. Med dette setup kan vi starte en `Task` for hver opgave i listen af spillere.
```csharp
var players = new List<string>() { "xXxkiller26xXx", "MurderHobo28", "fighter98" }
var highscores = Task.WhenAll(players.Select(p => GetHighscoreAsync(p)));
```
Listen `highscores` kommer til at indeholde alle spillernes highscores.

Dette er blot en af måderne hvorpå lister kan behandles asynkront. Der findes andre meetoder (f.eks. `IEnumerable.AsParallel` og `Parallel.ForEach`), som tjener lignende formål.

_Du kan også læse mere om `Task`s på [Microsofts Officielle Documentation](https://docs.microsoft.com/en-us/dotnet/api/system.threading.tasks.task?view=netstandard-2.0)_.
