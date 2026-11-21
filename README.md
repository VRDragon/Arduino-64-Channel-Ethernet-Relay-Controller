# 💡 Arduino Web Relay 64

**Arduino ako samostatný webserver: 64 výstupov ovládaných z prehliadača cez Ethernet, stránka je na SD karte.**

![Rok](https://img.shields.io/badge/rok-2017-blue) ![Arduino](https://img.shields.io/badge/Arduino-Mega-00979d) ![Sieť](https://img.shields.io/badge/Ethernet-HTTP%20%2B%20AJAX-blue)

> **Rok realizácie:** 2017 · **Tagy:** `arduino` `ethernet` `relay` `web-server` `ajax` `xml` `sd-card` `embedded` `home-automation` `led-lighting`

## O projekte

Ovládač pre prostredie **FORLED-SHOWROOM**, v ktorom bolo treba nezávisle zapínať desiatky svetelných okruhov. Namiesto počítača alebo servera beží celé riešenie priamo na mikrokontroléri: Arduino s Ethernet shieldom hosťuje HTML stránku z SD karty a spracúva príkazy z prehliadača.

## Funkcie

- 64 nezávislých výstupov (checkbox pre každý kanál).
- Žiadny externý server: HTTP server na porte 80 priamo v Arduine.
- Webové rozhranie sa načíta z SD karty (`index.htm`), takže sa dá meniť bez nahrávania firmvéru.
- AJAX (`XMLHttpRequest`): zmeny bez obnovy stránky.
- Stav výstupov sa vracia ako XML a synchronizuje každú sekundu.
- Statická IP adresa (predvolene `192.168.15.104`).

## Architektúra

```
Prehliadač ──HTTP/AJAX──► Ethernet ──► Arduino (HTTP server)
                                         ├── SD karta  → index.htm
                                         └── 64 GPIO výstupov → relé moduly
```

Prehliadač posiela požiadavky typu `LED1=1&LED2=0`, firmvér ich spracuje funkciou `SetLEDs()` a stav vráti cez `XML_response()`.

## Hardvér

Arduino Mega 2560 (alebo kompatibilné s dostatkom pinov), Ethernet shield so slotom na SD kartu, relé moduly s vlastným budičom a primerané napájanie. **GPIO piny nikdy nepripájajte priamo na cievky relé alebo záťaž.**

## Nasadenie

1. Otvorte `forled_server.ino` v Arduino IDE.
2. Skopírujte `index.htm` do koreňa SD karty.
3. Upravte MAC a IP adresu v kóde podľa svojej siete.
4. Nahrajte firmvér, vložte SD kartu a pripojte Ethernet.
5. Otvorte `http://192.168.15.104` v prehliadači.

## Známe obmedzenia

Ide o historický prototyp: mapovanie pinov má nezrovnalosti v indexovaní poľa, 64. kanál nemá priradený pin, stav výstupov je softvérový (bez spätnej väzby z relé) a server nemá autentifikáciu ani šifrovanie. Podrobný technický rozbor je v [docs/README.en.md](docs/README.en.md).

## Autor

**Vratislav Režo** ([@VRDragon](https://github.com/VRDragon))
