# Udstyr din Karakter

I denne opgaver skal vi udstyre vores karakter med rustning, våben og smykker. Hver af disse tilføjer en bonus til karakteren i en eller flere af de tre følgende attributter:
- Intellect
- Strength
- Agility

Spilleren kan være udstyret med følgende:
- Hjelm
- Amulet
- Rustning
- Skulderplader
- Bælte
- Busker
- Sko
- Handsker
- Ringe
- Våben

Spillerens rustning er delt op i fire grupper, som kan repræsenteres som sæt:
- Hoved (hjelm og amulet)
- Torso (rustning, skulderplader og bælte)
- Ben (Bukser og sko)
- Arme (Handsker, ringe og våben)

Vi har lavet en `Item`-klasse samt nogle Assets, som repræsenterer items i opgaven:
```csharp
[System.Serializable]
public enum ItemGroup
{
    Head, Torso, Legs, Hands
}

public class Item : ScriptableObject
{   
    public string Name;
    public int Intellect;
    public int Strength;
    public int Agility;
    public ItemGroup Group;

    public float IntellectMod;
    public float StrengthMod;
    public float AgilityMod;
}
```
Samt en `Group`-klasse, som repræsenterer de førnævnte grupper:
```csharp
public class Group
{
    public ItemGroup ItmGroup { get; set; }
    public List<Item> Items { get; set; }
}
```
Listen af alle items kan hentes ved brug af den statiske metode `Item.AllItems()` og alle grupperne kan hentes ved brug af `Item.GroupedItems()`.

## Opgaverne
1) Sammenregn den totalte bonus i de tre attributer som kan opnås med alle items i `Item.AllItems()`.
2) Forestil dig at rod-noden i hvert af de fire ovennævnte træer kan give en bonus til attributterne på de resterende stykker rustning.

    Eksempel: Hjelmen har give en attribut, som giver 15% ekstra strength (`Item.StrengthMod`) på alt rustning i dens gruppe (den selv inkluderet). Hjelmen giver 100 strength og amuletten giver 50. Total strength skal udregnes som:

        (100 * 1.15) + (50 * 1.15) = 172.5 ~ 172

    Udregn spillerens attributbonusser ud fra grupperne du finder i `Item.GroupedItems`.
