# Talenttræ
I denne opgave arbejder vi med talenttræer. Forestil dig et et træ med talenter eller skills, som hver især styrker spilleren.

<img src="images/skilltree.jpg" style="width: 300px; margin: auto; display: block;"  />

Hvert talent i træet giver spilleren en bonus i en eller flere af de følgende attributter:
- Intellect
- Strength
- Agility

Og hver talent i træet har en boolean `Picked`, som angiver om spilleren har burgt et point på talentet. Alle talenter har også en liste af deres sub-talenter, dvs. talenter, som spilleren først kan vælge efter et point er brugt i talentet.

## Opgaverne

1) Lav en datastruktur `MyTalent`, som skal indeholder talenter som beskrevet ovenfor.
1) I C# har vi lavet en klasse `Talent`. Der findes desuden en statisk property, som hedder `TalentTreeRoot`, som rod-nodet i et talent-træ. Gå igennem træet og sammenregn spillerens bonuser i de tre attributter. Et talent giver kun bonus, hvis det er `Picked`.
2) Udregn hvor meget bonus spilleren maksimalt kan få i en given attribut.