---
layout: default
title: Zpoždění spojů
permalink: /docs/realtime-api/zpozdeni-spoju
nav_order: 3
parent: Realtime API
---

## Původní informace o zpoždění spoje

Systém MPVnet detekuje průjezdy zastávkami a k nim dopočítává aktuální zpoždění, které pak používá jako predikci zpoždění i pro další zastávky. 
Nevýhodou tohoto systému je, že zpoždění pak může být velmi nepřesné, pokud má autobus dlouhý mezizastávkový úsek, nebo pokud mezi dvěma zastávkami na delší dobu uvízne.

## Výpočet zpoždění ke každé pozici spoje

Vylepšení tohoto algoritmu vyžaduje ke každému spoji k dispozici objekt ve formátu GTFS `shape`, tj. pole GPS bodů, které představují skutečnou trasu spoje. K jednotlivým bodům se vypočte předpokládaný čas průjezdu (buď jednoduchou interpolací, nebo výhledově složitějším algoritmem). Takto vypočtené přesnější zpoždění lze využít v informačních systémech na zastávkách, ve vozidlech, na webu a v aplikaci.

**Problematické body při určení pozice na trase:**
* trasa se překrývá (je tvořena stejnými body) při odbočce do menšího města a při návratu zpět
* trasa prochází kruhovým objezdem, kde se pak segmenty trasy i vícekrát překrývají
* trasa vede přes mimoúrovňové nájezdy na komunikace a tvoří "smyčky"

Tyto případy vedou k nemožnosti jednoznačně určit v jaké fázi trasy se spoj nachází. Řešením je používat informace o minulých pozicích spoje a zvolit takovou pozici na trase, která odpovídá uběhnuté době a minulé pozici na trase.
