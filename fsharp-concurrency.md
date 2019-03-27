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

Den ovenstående kode-snippet viser hvordan man skriver kode, som venter på et timer-event og udskriver til konsollen på samme tid. Dette er naturligvis et meget simpelt eksempel, men princippet ville være det samme hvis vi foreksempel ønskede at kontakte en server via en HTTP request uden at skulle fryse hele programmet.

Værd at bemærke er den lidt mystiske navngivning på `Async.RunSynchronously`, som blokerer den tråd der kalder metoden indtil et eller flere async workflows er færdige.