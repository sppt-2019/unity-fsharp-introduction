# Startopgave: 3rd Person Controller
I denne opgave skal du implementere en `3rd Person Controller` (se billedet). 

<img src="images/3rd_person.jpg" style="width: 400px; margin: auto; display: block;">

Vi har lavet en tom `ThirdPersonController` klasse i scenen `3rd Person Controller`, som du skal tilføje koden til.

## Opgaverne
1) Erklær et sæt af variable, som du kan ændre fra Unity's Editor. Disse variable kunne f.eks. styre hastigheden hvormed spilleren bevæger sig og hvor højt han hopper.
2) Skriv kode, som tillader spilleren at bevæge sig rundt i verdenen med piletasterne eller WASD.
3) Rotere kameraet rundt om spilleren ved bevægelse af musen.
4) Få spilleren til at hoppe når der trykkes på 'Space'-knappen.

# Hints
For at rotere kameraet rundt om spilleren skal du rotere kamerat omkring `Vector3.up` i det horisontale plan og kameraets egen `transform.right` på det verticale plan.