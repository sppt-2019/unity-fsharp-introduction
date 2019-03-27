# AI i Strategispil

I denne opgave skal du lave en state machine, som repræsenterer en kamptrop i et realtidsstrategispil. Vi har implementeret logikken for en bueskytte med følgende states:

- **Bevæg**: Bueskytten bevæger sig mod en position. Når bueskytten når frem til positionen begynder den at angribe tårnet i midten.
- **Angriber**: Bueskytten angriber tårnet i midten. Efter fem skud begynder bueskytten at bevæge sig mod en ny position.
- **Flygter**: Bueskytten er blevet ramt af et projektil og flygter i vildskab.

Der er dog en hage: I stedet for at følge den klassiske tankegang om at hver kamptrop har sin egen state machine, skal du tænke på det som en state machine, der har en samling af alle kamptropper som på nuværende tidspunkt er i den givne state. Det er derfor state machinen der sørger for at opdatere alle kamptropper i dens samling.

<img src="images/warcraft.jpg" style="width: 400px; margin: auto; display: block;" />

Når spillet starter bliver 10 bueskytter spawnet på tilfældige positioner i spilverdenen. De vil alle sammen kalde `JoinState` i deres `Start`-metode.

## Opgaverne
1) Opsaml referencer til alle bueskytterne når de kalder `JoinState` i en samling.
2) Implementer den omvendte state machine.
3) På samplesheet'et finder du eksempler på hvordan jobs kan paralleliseres i det sprog du arbejder i. Unity tillader lige nu ikke opdateringer uden for Main-tråden, men forsøg alligevel at tilføje en `UpdateParallel`-metode, hvor du implementerer en parallel løsning, som opdaterer alle bueskytter parallelt.