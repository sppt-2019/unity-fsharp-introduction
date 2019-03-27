# Dialogtræ

I denne opgave skal du konstruere en datastruktur, som repræsenterer en dialog med en ikke-spilbar karakter i dit spil. Når dialogen med karakteren startes skal karakteren starte med at sige noget. Spilleren kan svare på dette med en til flere valgmuligheder.

Når samtalen er færdig får spilleren en konsekvens alt efter hvor pænt eller grimt han har snakket til karakteren. Disse konsekvenser er:
- Karakteren bliver fjendtlig og angriber
- Karakteren bliver spillerens følgesvend
- Karakteren giver spilleren en gave
- Ingen reaktion
- Samtalen går til et andet punkt

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
1) Lav en datastruktur som kan indeholde et tree som dækker over alle mulighederne i sådan en dialog.
2) Vi har lavet en klasse `Node`:
```csharp
public enum DialogOutcome
{
    Continues,
    Companion,
    Hostile,
    Gift,
    None
}

public class Node
{
    public static List<Node> GetExampleDialog()
    {
        return new List<Node>
        {
            new Node("welcome", "Velkommen til vores lille by, hvad kan jeg hjælpe med?",
                new List<(string, string)>{("work", "Jeg leder efter arbejde..."),
                    ("found", "Jeg fandt denne hund ude i skoven, kender du ejeren?"), ("move", "Flyt dig!")}),
            new Node("work",
                "Min hund er blevet væk og jeg skulle alligevel ud og lede efter den. Har du brug for hjælp?",
                new List<(string,string)> {("yes", "Ja"), ("no", "Nej")}),
            new Node("yes", "Fantastisk, mit sværd er dit!", DialogOutcome.Companion),
            new Node("no", "Ærgeligt, sig til hvis du ombestemmer dig.", ("welcome", "Ok")),
            new Node("found", "MIN LILLE HUND! Den har været væk i flere dage. Tusind tak!",
                DialogOutcome.Gift),
            new Node("move", "Her i byen taler vi pænt til hinanden!",
                new List<(string,string)> {("die", "Så skal byen DØØØØØØØ!"),
                    ("sorry", "Undskyld. Det har været en dårlig dag")}),
            new Node("die", "VI BLIVER ANGREBET!", DialogOutcome.Hostile),
            new Node("sorry", "Det er i orden. Det kan ske for alle", ("welcome", "Tak")),
            new Node("goodbye", "Held og lykke på dine rejser", DialogOutcome.None)
        };
    }

    public string Name { get; }
    public string Line { get; }
    public List<(string nodeToGoTo, string reply)> ChildNames { get; set; }
    public DialogOutcome Outcome { get; set; } = DialogOutcome.Continues;
}
```

Den statiske `GetExampleDialog`-metode returnerer ovenstående dialog som en liste. Dialogen skal omdannes til et træ, så ledes at `Node`n med navnet "work" bliver barn af noden med navnet "welcome", da "work" er en af elementerne i "welcome"'s `ChildNames`.


3) Skriv en funktion som kan finde alle unikke stier, som fører til at spilleren får en gave.

4) Omform listen til et dialogtræ som gemmes i din egen datastruktur efter ovenstående beskrivelse.
5) Lav noget kode som kan finde alle unikke stier, som fører til at spilleren får en gave.
