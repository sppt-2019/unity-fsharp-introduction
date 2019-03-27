# Dialogtræ

I denne opgave skal du konstruere en datastruktur, som repræsenterer en dialog med en ikke-spilbar karakter i dit spil. Når dialogen med karakteren startes skal karakteren starte med at sige noget. Spilleren kan svare på dette med en til flere valgmuligheder.

Når samtalen er færdig får spilleren en konsekvens alt efter hvor pænt eller grimt han har snakket til karakteren. Disse konsekvenser er:
- Karakteren bliver fjendtlig og angriber
- Karakteren bliver spillerens følgesvend
- Karakteren giver spilleren en gave
- Ingen reaktion
- Samtalen går til et andet punkt

<img src="images/conversation.jpg" style="width: 300px; margin: auto; display: block;">

## Eksempel på samtale:

Velkommen til vores lille by, hvad kan jeg hjælpe med?
    
1) Jeg leder efter arbejde...
    
    Min hund er blevet væk og jeg skulle alligevel ud og lede efter den. Har du brug for hjælp?
    1) Ja.

        Fantastisk, mit sværd er dit! 
        --> *Karakteren bliver ens følgesvend*
    2) Nej.

        Ærgerligt, sig til hvis du ombestemmer dig. 
        --> *Ingen reaktion*
2) Jeg fandt denne hund ude i skoven, kender du ejeren

    MIN LILLE HUND! Den har været væk i flere dage. Tusind tak --> *Karakteren giver spilleren en gave*
3) Flyt dig!

    Her i byen taler vi pænt til hinanden!
    1) Så skal byen DØØØØØØØ!
        
        VI BLIVER ANGREBET! --> *Karakteren bliver fjendtlig*
    2) Undskyld. Det har været en dårlig dag.
    
        Det er i orden. Det kan ske for alle. --> *Ingen reaktion*

## Opgaverne
1) Lav en datastruktur som kan indeholde denne slags dialoger.
2) Vi har lavet en klasse `Node`, med en static metode `GetTree`, som returnerer hele dialogen i en liste. En `Node` har fire properties: `Name`, `Line`, `ChildNames` og `Outcome`. Det er din opgave at omdanne denne liste til et træ, som passer i den datastruktur du definerede i opgave 1). Eksempel: 

Node 1:
- Name: "greet"
- Line: "Goddag min ven. Vil du handle med mig?"
- ChildNames: [(nodeToGoTo: "shop", reply: "Ja tak. Jeg mangler mælk og ost."),(nodeToGoTo: "exit", reply: "Nej tak")]
- Outcome: DialogOutcome.None

Node 2:
- Name: "shop"
- Line: "Det var heldigt. Jeg malkede køerne i morges. Du kan få mælken gratis"
- ChildNames: []
- Outcome: DialogOutcome.Gift

Disse to `Node`s skal sættes sammen, så Node 2 bliver et barn af Node 1.

3) Lav noget kode som kan finde alle unikke stier, som fører til at spilleren får en gave.