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
        --> *Samtalen går til "Jeg leder efter arbejde..."*
2) Jeg fandt denne hund ude i skoven, kender du ejeren

    MIN LILLE HUND! Den har været væk i flere dage. Tusind tak --> *Karakteren giver spilleren en gave*
3) Flyt dig!

    Her i byen taler vi pænt til hinanden!
    1) Så skal byen DØØØØØØØ!
        
        VI BLIVER ANGREBET! --> *Karakteren bliver fjendtlig*
    2) Undskyld. Det har været en dårlig dag.
    
        Det er i orden. Det kan ske for alle. --> *Samtalen går til "Jeg leder efter arbejde..."*

4) Farvel

    Held og lykke på dine rejser. --> *Ingen reaktion*

## Opgaverne
1) Lav en datastruktur som kan indeholde denne slags dialoger.
2) Lav noget kode som kan finde alle unikke muligheder fra dialogens start til slut. Alle spillerens muligheder i dialogen må kun vælges én gang.