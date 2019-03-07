---
layout: default
title: Další využití
permalink: /docs/realtime-api/dalsi-vyuziti
nav_order: 10
parent: Realtime API
---

## Predikce zpoždění

Kromě zlepšení samotného výpočtu aktuálního zpoždění je další možností zpřesnění odhadu budoucího vývoje na trase spoje. V současnosti informační tabule na zastávce zobrazuje u každého spoje jeho aktuální zpoždění, přitom pro cestujícího je zajímavější informace o tom, jaké je předpokládané zpoždění na zastávce, kde čeká. To lze výrazně zpřesnit.

## Využití historických dat

Využití historických dat staví na předpokladu, že pokud stejný spoj na nějakém úseku v posledních dnech navýšil zpoždění např. o 5-7 minut, bude se toto pravděpodobně opakovat i den následující a cestujícímu vyčkávajícímu na konci tohoto úseku tak můžeme předpovědět, že spoj pojede spíše později.

## Využití aktuálních dat

Zpoždění se v místě a čase většinou zásadněji nemění, takže je možné pro zpřesnění odhadu využít též data z jiných spojů, které jedou stejným/podobným úsekem a projely jej těsně před spojem, který se snažíme odhadnout.

## Detekce mimořádných událostí

Výše zmíněné predikce stále budou trochu pomalejší v případě náhlých problémů (např. nehody blokující komunikaci). Z pohybu vozidel je nicméně možné některé situace automaticky detekovat. Příklady takovýchto "podezřelých" situací jsou následující
* Vozidlo, nebo více vozidel, se dlouho nehýbe z místa
* Vozidlo jede mimo stanovenou trasu
* Vozidlo nedodržuje jízdní řád (jede příliš brzy/pozdě)

V případě napojení na další zdroje dat (např. informace o provozu z NDIC) pak lze využít též informace o uzavírkách a nehodách, které mohou detekci problémů zpřesnit.

Detekce problémů umožní rychle informovat jak cestující, tak i řídící pracovníky (dispečery).
