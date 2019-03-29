# Concurrency i F#

.NET platformen har en del forskellige værktøjer, som du kan bruge når du arbejder med concurrency. Disse inkluderer `Thread`s og `BackgroundWorker`s. I dette dokument vil vi præsentere en concurrency strategi ved navn Async Workflows, som er specifik for F#. Du kan også bruge `Task`s, som er rigtigt smarte. Dem kan du læse mere om i vores [Concurrency i C# Guide](csharp-concurrency.md).

Bloggen _F# for Fun and profit_ har en rigtig god beskrivelse af [asynchronous programming in F#](https://fsharpforfunandprofit.com/posts/concurrency-async-and-parallel/), som vi drager inspiration fra i denne gennemgang. Hvis du vil vide mere anbefaler vi derfor at starte der.

## Asynchronous Workflows

Asynchronous workflows er designet til at gøre det nemmere at skrive asynkron kode. Lad os starte med et eksempel:

```fsharp
open System

let userTimerWithAsync =
    // Start en timer, som tæller ned fra 2 sekunder og affyrer et event når den når 0
    let timer = new System.Timers.Timer(2000.0)
    //Her laver vi et async workflow, som venter på eventet fra timeren og med handleren Async.Ignore
    let timerEvent = Async.AwaitEvent (timer.Elapsed) |> Async.Ignore
    //Start eventet
    printfn "Waiting for timer at %O" DateTime.Now.TimeOfDay
    timer.Start()

    //Dette kode bliver kørt mens timeren "arbejder" i baggrunden
    printfn "Doing something useful while waiting for event"

    //Og her synkroniserer vi med timeren, altså blokerer tråden indtil timeren når 0
    Async.RunSynchronously timerEvent
```

Den ovenstående kode-snippet viser hvordan man skriver kode, som venter på et timer-event og udskriver til konsollen på samme tid. Dette er naturligvis et meget simpelt eksempel, men princippet ville være det samme hvis vi for eksempel ønskede at kontakte en server via en HTTP request uden at skulle fryse hele programmet.

Værd at bemærke er den lidt mystiske navngivning på `Async.RunSynchronously`, som blokerer den tråd der kalder metoden indtil et eller flere async workflows er færdige.

## Implementer et Async Workflow
Du kan også implementere dine egne `async` funcktioner. Dette gøres ved at deklarere en funktion, hvor funktionskroppen er pakket ind i `async {}`:
```fsharp
let emulateAsyncWork workTimeMs  = async{
    printfn "Starting async work emulation at %O" DateTime.Now.TimeOfDay
    do! Async.Sleep workTimeMs
    printfn "Async work done at %O" DateTime.Now.TimeOfDay
    }
```

Async workflows kan også nestes:
```fsharp
let nestedWorkflow  = async{
    printfn "Starting parent"

    //Start et nested workflow og lad det få en smule køretid
    let! childWorkflow = Async.StartChild sleepWorkflow
    do! Async.Sleep 100

    //På dette tidspunkt kører to async workflows samtidigt
    printfn "Doing something useful while waiting "

    //let! blokerer workflow'et indtil childWorkflow er færdigt
    let! result = childWorkflow

    //Her kan vi tilgå resultatet fra 'childWorkflow' som 'result'
    printfn "Finished parent"
    }

// run the whole workflow
Async.RunSynchronously nestedWorkflow
```
I ovenstående eksempel laver vi et nested workflow. I eksemplet starter vi et workflow, som starter et nyt workflow. Disse to kører på sammetid indtil de synkroniseres med `let!`.

## Concurrent Liste Operationer
I F# er det muligt at lave concurrent liste operationer, alstå kode som arbejder med alle elementerne af en liste samtidigt. Dette er især værdifuldt når der skal laves en større beregning på hvert element af listen.

I nedenstående eksempel forestiller vi os at vi vil hente en række spilleres resultater i et spil fra en server. Vi antager at `DownloadPlayerHighscore` er et async workflow, som tager en spillers brugernavn og returnerer dennes highscore:

```fsharp
//DownloadPlayerHighscore:(string -> int)

let GetHighscoreList (players:list string) =
    players
    |> List.map DownloadPlayerHighscore
    |> Async.Parallel
    |> Async.RunSynchronously

let highscores = GetHighscoreList ["thelegend225";"imbaman";"ninjaboi69"]
```

I ovenstående eksempel bruger vi en `List.map`, som omdanner listen af spillernavne til en liste af async workflows. Denne liste bliver givet videre til `Async.Parallel` funktionen, som kører alle async workflows i listen på sammetid og tilsidst bliver listen af async workflows sendt videre til `Async.RunSynchronously`, som blokerer indtil alle async workflows er færdige.
