---
layout: default
title: Popis formátu GTFS
description: "Popis tabulek a jejich atributů."
permalink: /docs/jizdni-rady/GTFS
nav_order: 1
parent: Jízdní řády
---

## Jízdní řády ve formátu GTFS (převzato z [webu PID](https://pid.cz/o-systemu/opendata/))

Soubor obsahuje jízdní řády všech linek PID (metro, tramvaje, autobusy, lanovka, přívozy, vlaky), trasy spojů, informace o tarifu pro výpočet ceny jízdného a garantované návaznosti mezi spoji. Jízdní řády vlaků jsou však zatím pouze experimentální, mohou obsahovat nepřesnosti v případě krátkodobých výluk a některých svátečních dnů!

Data splňují specifikaci GTFS Static s rozšířením Trip-to-trip transfers popisující garantované (vyčkávací) přestupní vazby. Specifika souboru s jízdními řády PID jsou vypsána níže. Tato specifika slouží jen pro lepší pochopení a lidskou čitelnost dat, případně poskytují doplnění pro správné použití těchto dat, na samotnou validitu nemají vliv.

### stops.txt

V souboru jsou pouze zastávky, které jsou aktuálně využity, tj. alespoň v některý den platnosti feedu v zastávce zastavují nějaké spoje.

Stanice metra a vlaků mají dvě zastávky (pro každý směr jednu) sdružené do jedné stanice. `stop_id` této stanice je ve tvaru `Učíslo_uzluSindex`. U přestupních stanic metra jsou pak zastávky čtyři, rozdělené do dvou stanic, viz níže.

```csv
U1072S1,“Můstek – A“,50.08353,14.42456,“P“,,1,,1
U1072S2,“Můstek – B“,50.08341,14.42339,“P“,,1,,1
U1072Z101P,“Můstek – A“,50.08312,14.42496,“P“,,0,U1072S1,1,M1
U1072Z102P,“Můstek – A“,50.08394,14.42415,“P“,,0,U1072S1,1,M2
U1072Z121P,“Můstek – B“,50.08321,14.42279,“P“,,0,U1072S2,1,M3
U1072Z122P,“Můstek – B“,50.08361,14.42398,“P“,,0,U1072S2,1,M4
```

`zone_id` popisuje pásmové zařazení zastávky (P, 0, B, 1, 2, … ale i např. „B,1“). Může být prázdné pro linky se zvláštním tarifem (např. AE). Protože v jedné zastávce mohou být různé linky zařazeny do různých tarifních pásem, může kvůli tomu být zastávka rozštěpena do více „virtuálních“ záznamů, které všechny sdílí stejnou pozici v mapě. Toto je potřebné například pro výpočet ceny. Fyzicky je pak na místě pouze jeden sloupek. Níže příklad výstupní zastávky na Černém Mostě.

```csv
U897Z1P,“Černý Most“,50.10912,14.57713,„P“,…,V1    <– pro městské linky (MHD)
U897Z1,“Černý Most“,50.10912,14.57713,„0“,…,V1      <– pro příměstské linky v pásmu 0
U897Z1N,“Černý Most“,50.10912,14.57713,„“,…,V1      <– pro linku IKEA (bezpl. přeprava)
U897Z21,“Černý Most“,50.10912,14.57713,„B,1“,…,V1 <– pro příměstské linky v pásmu B,1
```

`platform_code` je kód stanoviště používaný k označení sloupku v rámci skupiny zastávek
Jedna zastávka může mít i více záznamů, pokud se v průběhu feedu mění některá z jejích vlastností (například bezbariérovost, nebo poloha)

### routes.txt

`route_short_name` odpovídá označení linky pro veřejnost (např. „A“, „22“, „S9“, „P7“).
`route_long_name` odpovídá licenční trase linky (nemusí nutně souhlasit s reálnou trasou například v případě výluky!)

### trips.txt

`block_id` je konstruováno jako trip_id prvního spoje v sekvenci spojů, které na sebe navazují. Viz níže příklad spoje linky 174, který na Lukách mění číslo a pokračuje dále jako spoj linky 301 do Chýnice. Přes block_id jsou propojeny pouze spoje, kde vozidlo pokračuje dále a cestující tak nemusí přestupovat. Garantované návaznosti s přestupem zachycují transfers.txt.

```csv
L174D1,1111100-1,174_17_180509,“Luka“,0,174_17_180509,L174V6,1,2,1,0
L301D1,1111100-1,301_2_180509,“Ořech“,0,174_17_180509,L301V1,1,2,1,0
```

Speciálním případem použití `block_id` jsou výjezdové a zátahové spoje tramvají z/do vozovny, které přepravují cestující. Pokud vozovna není přímo na trase linky, je spoj roztržen v poslední/první zastávce na pravidelné trase linky na dva samostatné tripy propojené přes `block_id`. Příklad níže ukazuje zátah linky 5 ze Sídliště Barrandov do Vozovny Motol (poslední zastávka na trase je Anděl).

```csv
L5D1,0000100-1,5_1_180511,“Vozovna Motol“,0,5_1_180511,L4V18,2,0,0,1
L5D1,0000100-1,5_2_180511,“Vozovna Motol“,0,5_1_180511,L4V19,2,0,0,8
```

`trip_operation_type` rozlišuje regulérní spoje (hodnota = 1) od výjezdů, zátahů a přejezdů s cestujícími mimo běžnou trasu linky (hodnota > 1). Spoje, které zatahují do vozovny na své trase, jsou vedeny jako regulérní. Spoje, které z trasy sjíždějí, jsou rozděleny na regulérní a neregulérní část, viz příklad výše. To se týká pouze tramvají, v ostatních trakcích jsou takové spoje neveřejné, protože cestující nepřepravují.

```
1 = regulérní spoj na trase
7 = výjezd mimo pravidelnou trasu
8 = zátah mimo pravidelnou trasu
9 = přejezd na lince mimo pravidelnou trasu
10 = přejezd na jinou linku
```

### calendar.txt

`service_id` obsahuje řetězec sedmi nul a jedniček, které pro každý den v týdnu udávají, kdy spoj jede pro vyšší lidskou čitelnost. Platnost je vždy minimálně 10 dní ode dne vygenerování.

### transfers.txt

Soubor obsahuje pouze garantované přestupy mezi spoji za použití rozšíření Trip-to-trip transfers. Garantovaný přestup je takový, kde je navazující spoj povinen vyčkat příjezdu návazného spoje. Typicky je to omezeno limitem, do kdy musí návazný spoj přijet, aby se nevyčkávalo příliš dlouho. Tento limit v datech uveden není.
