---
layout: default
title: Převod číselníku spojů z CIS na GTFS
permalink: /docs/realtime-api/prevod-CIS-GTFS
nav_order: 2
parent: Realtime API
---

# Převod číselníku spojů z CIS na GTFS

## Dohledávání spojů MPVnet v jízdních řádech GTFS

Pro další zpracování pozic spojů je potřebné získat identifikátor spoje ve formátu GTFS. To lze dohledat komplexním SQL query, který na základě času odjezdu z první zastávky vyhledá odpovídající spoj v JŘ.

**Postup**
* vyberou se pouzy ty spoje `trip_id`, které jsou uvedeny v daném dni jako platné dle `calendar_dates`

```sql
SELECT trips.trip_id,routes.route_id,routes.route_short_name,trips.wheelchair_accessible,trips.trip_headsign FROM trips
INNER JOIN routes ON trips.route_id=routes.route_id
INNER JOIN stop_times ON trips.trip_id=stop_times.trip_id
WHERE original_route_id=? AND time({} departure_time)=time(?)
AND stop_id IN (SELECT stop_id FROM stops WHERE stop_id LIKE ? AND platform_code=? AND stop_sequence=1)
AND service_id IN (
    SELECT service_id FROM calendar WHERE {}=1
    AND date(substr(start_date, 1, 4)||'-'||substr(start_date, 5, 2)||'-'||substr(start_date, 7, 2))<=date(?)
    AND date(substr(end_date, 1, 4)||'-'||substr(end_date, 5, 2)||'-'||substr(end_date, 7, 2))>=date(?)
    UNION SELECT service_id FROM calendar_dates WHERE exception_type=1
        AND date(substr(date, 1, 4)||'-'||substr(date, 5, 2)||'-'||substr(date, 7, 2))=date(?)
    EXCEPT SELECT service_id FROM calendar_dates WHERE exception_type=2
        AND date(substr(date, 1, 4)||'-'||substr(date, 5, 2)||'-'||substr(date, 7, 2))=date(?)
)
```
