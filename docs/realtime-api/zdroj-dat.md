---
layout: default
title: Zdroj dat
permalink: /docs/realtime-api/zdroj-dat
parent: Realtime API
---

# Data o polohách ze systému MPVnet

Systém MPVnet, který provozuje ROPID, umožňuje v rámci komunikace s ostatními dopravními dispečinkovými systémy zahrnout přeposílání informací o polohách spojů systému, který vystaví API endpoint pro přijímání XML zpráv.

Mapa aktuálních pozic spojů, které poskytuje přímo MPVnet skrze [webovou aplikaci](https://www.mpvnet.cz/pid/map).

Aktualizace stavů spojů pro každý spoj je zpravidla v 10-20 sekundovém intervalu a obsahuje následující atributy:

- označení linky v CIS (`lin`, `alias`, `spoj`, `po`)
- doplňující informace ke spoji (`t`, `sled`, `np`, `zrus`)
- čas aktualizace a pozice spoje ve WGS84 (`cpoz`, `lat`, `lng`)
- poslední zaznamenaný průjezd zastávkou (`zast`, `zpoz_prij`, `zpoz_odj`)
- seznam zastávek dle JŘ s naměřeným zpožděním popř. jeho predikcí s atributy:
   - označení zastávky v CIS (`zast`, `stan`)
   - časy příjezdu a odjezdu dle JŘ (`prij`, `odj`)
   - zpoždění (`zpoz_typ`, `zpoz_prij`, `zpoz_odj`)

Aktualizace přichází každých 5-20s v dávkách ve formátu XML na předem definovaný API endpoint Datové platformy.
