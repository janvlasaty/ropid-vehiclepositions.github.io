---
layout: default
title: Převodník číselníku CIS zastávek na formát GTFS
description: "Popis tabulek a jejich atributů."
permalink: /docs/jizdni-rady/CIS-zastavky
parent: jizdni-rady
---

## Seznam zastávek PID (převzato z [webu PID](https://pid.cz/o-systemu/opendata/))

Soubory se seznamem zastávek poskytují podrobnější informace o zastávkových sloupcích, které se nevejdou do feedu GTFS, který navíc neumožňuje strukturování dat. K dispozici je ve formátech XML a JSON, které jsou obsahem odpovídající. K XML formátu existuje XML schema, které formát popisuje. Níže jsou stručně popsány jednotlivé entity.

### Skupiny `<group>`

Skupina definuje všechny zastávky ve stejném uzlu, které mají stejný název. Unikátním identifikátorem skupiny je trojice (`name`, `districtCode`, `isTrain`), nebo dvojice (`idosName`, `isTrain`), nebo název `uniqueName`.

`name` je společný název zastávek ve skupině. Pozor, může existovat více skupin se stejným názvem, viz příklad níže.
`districtCode` je „SPZ“ kód okresu, ve kterém zastávka leží.
`isTrain` je true, pokud jde o vlakovou stanici. Výchozí hodnota je `false`.
`idosName` je název používaný v aplikacích (IDOS aj.)
`fullName` je plný název zastávek s rozepsanými zkratkami („rozc.“ -> „rozcestí“ apod.)
`uniqueName` je název zastávek, který je unikátní v rámci celého souboru. Vychází z `idosName`, kde v případě kolize vlakové a nevlakové stanice přidá k názvu vlakové text „(vlak)“.
`node` je číslo uzlu, do kterého zastávky patří. Pozor, toto číslo není unikátním identifikátorem názvu zastávky, protože jeden uzel může obsahovat více zastávek různého názvu. Příkladem je uzel 237, který sdružuje zastávky Karlovo náměstí, Palackého náměstí, Moráň a Novoměstská radnice. Všechny tyto skupiny zastávek mají stejné číslo uzlu.
`avgLat`, `avgLon`, `jtskX`, `jtskY` je GPS/JTSK pozice agregovaná ze všech sloupků.
`municipality` je název města/obce, na jejímž území zastávka leží.

#### Příklad 1: Skupiny zastávek s názvem „Chrášťany“ různého typu v různých okresech (pouze vybrané atributy)

```xml
<group name=“Chrášťany“ districtCode=“RA“ isTrain=“true“ idosName=“Chrášťany“ uniqueName=“Chrášťany“ node=“9520″ avgLat=“50.1428″ avgLon=“13.6631536″ municipality=“Chrášťany“>

<group name=“Chrášťany“ districtCode=“BN“ idosName=“Chrášťany (BN)“ uniqueName=“Chrášťany (BN)“ node=“4471″ avgLat=“49.7927″ avgLon=“14.5866871″ municipality=“Chrášťany“>

<group name=“Chrášťany“ districtCode=“KO“ idosName=“Chrášťany (KO)“ uniqueName=“Chrášťany (KO)“ node=“2410″ avgLat=“50.06581″ avgLon=“14.9295769″ municipality=“Chrášťany“>

<group name=“Chrášťany“ districtCode=“PZ“ idosName=“Chrášťany (PZ)“ uniqueName=“Chrášťany (PZ)“ node=“1190″ avgLat=“50.0447922″ avgLon=“14.2591076″ municipality=“Chrášťany“>
```

#### Příklad 2: Skupiny zastávek v uzlu 237 (pouze vybrané atributy)

```xml
<group name=“Karlovo náměstí“ node=“237″ avgLat=“50.07532″ avgLon=“14.4185734″>

<group name=“Moráň“ node=“237″ avgLat=“50.0740242″ avgLon=“14.41873″>

<group name=“Novoměstská radnice“ node=“237″ avgLat=“50.0774574″ avgLon=“14.4195213″>

<group name=“Palackého náměstí“ node=“237″ avgLat=“50.07274″ avgLon=“14.4144449″>
```

### Zastávky (sloupky) `<stop>`

Každá skupina zastávek stejného názvu obsahuje zastávkové sloupky.

`id` je interní identifikátor zastávky číslo uzlu / číslo sloupku. Tento identifikátor je unikátní v rámci celého souboru a neměnný mezi aktualizacemi souboru. Číslo uzlu odpovídá hodnotě `node` skupiny zastávek.
`platform` je kód stanoviště používaný k označení sloupku v rámci skupiny zastávek například na odjezdových tabulích
`altIdosName` je název, který může obsahovat upřesnění, jehož účelem je bližší identifikace konkrétního sloupku, viz příklad níže

```xml
<group name=“Anděl“ districtCode=“AB“ idosCategory=“301003″ idosName=“Anděl“ uniqueName=“Anděl“ node=“1040″>
<stop id=“1040/1″ platform=“A“ altIdosName=“Anděl (ul. Plzeňská)“ lat=“50.07193″ lon=“14.40363″ />
<stop id=“1040/2″ platform=“B“ altIdosName=“Anděl (ul. Plzeňská)“ lat=“50.0719528″ lon=“14.4028063″ />
<stop id=“1040/3″ platform=“C“ altIdosName=“Anděl (ul. Nádražní)“ lat=“50.071804″ lon=“14.4042273″ />
<stop id=“1040/4″ platform=“D“ altIdosName=“Anděl (ul. Nádražní)“ lat=“50.0709763″ lon=“14.40455″ />
<stop id=“1040/11″ platform=“K“ altIdosName=“Anděl“ lat=“50.0717354″ lon=“14.4019508″ />
<stop id=“1040/12″ platform=“L“ altIdosName=“Anděl (ul. Stroupežnického)“ lat=“50.0715″ lon=“14.4029284″ />
<stop id=“1040/14″ platform=“N“ altIdosName=“Anděl (ul. Stroupežnického)“ lat=“50.0715179″ lon=“14.4026489″ />
<stop id=“1040/101″ platform=“M1″ altIdosName=“Anděl“ lat=“50.06953″ lon=“14.4035711″ />
<stop id=“1040/102″ platform=“M2″ altIdosName=“Anděl“ lat=“50.070488″ lon=“14.4048777″ />
</group>
```

### Projíždějící linky `<line>`

Každá zastávka obsahuje seznam linek, které v ní zastavují:

`id` je číselný identifikátor linky.
`name` je označení linky používané ve vztahu k cestujícím
`type` je typ dopravního prostředku (možné hodnoty viz XSD)
`isNight` je `true`, pokud jde o noční linku
`direction` je nejčastější konečná pro spoje odjíždějící z dané zastávky
`direction2` je alternativní konečná, pokud jsou spoje pásmované (nemusí být vyplněno)

