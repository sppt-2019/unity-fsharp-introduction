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