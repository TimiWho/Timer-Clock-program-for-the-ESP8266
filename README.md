# Timer-Clock-program-for-the-ESP8266

[Kurze Änderungsübersicht](AENDERUNGEN_README_2026-10-21.md)

## 🚀 TimerClock 3.1 – vier Zeit-/Astrokanäle

![Plattform](https://img.shields.io/badge/Plattform-ESP8266-blue) ![Schnittstelle](https://img.shields.io/badge/Schnittstelle-HTTP%20/%20GPIO-orange)

ESP8266-Timerprojekt von John Halfords mit vier unabhängig einstellbaren Kanälen, Weboberfläche, Sonnenauf-/untergang und ausgehenden HTTP-Kommandos. Autoren- und Quellenangaben der bisherigen README bleiben erhalten.

**Dokumentationsstand:** 21.10.2026 · Branch `master`. Beschreibt den vorhandenen Projektstand; kein neuer Funktionstest am Gerät.

[Quellcode herunterladen](https://github.com/TimiWho/Timer-Clock-program-for-the-ESP8266/archive/refs/heads/master.zip) · [Ursprüngliche Haupt-README](README_Backup_2026-10-21.md)

---

## ⚙️ Software und Datenfluss

`TimerClock_31.ino` enthält Webserver, Schaltlogik und Zeitberechnung; `UserDeclarations.h` die Benutzerkonfiguration. Jeder Timer a–d besitzt Ein- und Ausschaltzeit, GPIO-Zuordnung und optional einen HTTP-Aufruf. Zeiten können direkt oder aus Sonnenauf-/untergang mit Offset abgeleitet werden.

Die Ausgangsbezeichner `timer_a_on/off` bis `timer_d_on/off` bezeichnen getrennte GPIO-Ausgänge für Ein-/Aus-Impulse. In `UserDeclarations.h`: a = GPIO15/13 (D8/D7), b = GPIO12/14 (D6/D5), c = GPIO0/4 (D3/D2), d = GPIO5/16 (D1/D0). `wifiindicator=2` ist GPIO2/D4; `myBaudrate=19200`, `DebugLevel=1`. Diese Benutzerwerte entsprechen keinem anderen SSR-/Relaisprojekt. Weboberfläche und Einstellungen sind die Eingänge, GPIO-Pegel und HTTP-Aufrufe die Ausgänge. Ein MQTT-JSON-Protokoll ist nicht implementiert.

Die ursprüngliche Dokumentation nennt ESP8266-Core 2.7.4, ezTime und Dusk2Dawn. Diese Angaben sind der ursprüngliche Entwicklungsstand, keine Freigabe beliebiger neuer Bibliotheksversionen. EEPROM speichert Einstellungen; der HTTP-UpdateServer stellt den Updatepfad bereit.

## 🔌 Ein-/Ausgänge, Konfiguration und Timer

Die Bezeichner entsprechen dem Quellcode. Pinangaben `D0`–`D8` sind Boardnamen, keine GPIO-Nummern. Initialwerte von Zeitparametern sind bei `millis()`-Vergleichen Millisekunden; abweichende Einheiten sind gesondert angegeben. Laufzeit-Zeitstempel und Zähler sind keine zusätzlichen festen Wartezeiten.

### [TimerClock_31](TimerClock_31.ino)

**Konfiguration:** [UserDeclarations.h](UserDeclarations.h). Diese tatsächlich eingebundenen Header haben Vorrang vor dem Fallback im Sketch.

**Timer und relevante Initialparameter:**

| Bezeichner | Initialwert | Bedeutung / Einheit |
| --- | --- | --- |
| `timer_a_on` | `15` | Friendly name for GPIO15 (=D8)  . |
| `timer_a_off` | `13` | Friendly name for GPIO13 (=D7)   . |
| `timer_b_on` | `12` | Friendly name for GPIO12 (=D6)    . |
| `timer_b_off` | `14` | Friendly name for GPIO14 (=D5)     . <- Change these to your hardware design if neccessary |
| `timer_c_on` | `0` | Friendly name for GPIO0  (=D3)    . |
| `timer_c_off` | `4` | Friendly name for GPIO4  (=D2)   . |
| `timer_d_on` | `5` | Friendly name for GPIO5  (=D1)  . |
| `timer_d_off` | `16` | Friendly name for GPIO16 (=D0) . |
| `TimerOn_IN_MINUTES` | `60 * HourTimerOnTemp` | Number of hours times 60 |
| `TimerOff_IN_MINUTES` | `60 * HourTimerOffTemp` | Number of hours times 60 |

## 🐞 GitHub-Issues

**Abgleich:** 21.10.2026. Status aus GitHub; diese Übersicht ändert keine Issues.

Zum Abgleichzeitpunkt sind keine offenen GitHub-Issues vorhanden.

In diesem Repository sind zum Abgleichzeitpunkt keine GitHub-Issues vorhanden.

## 🗂️ Originaldokumentation und Autorenhinweise

Die ursprünglichen technischen Erläuterungen, Quellenangaben und Autorenhinweise bleiben nachfolgend erhalten. Maßgeblich für konkrete Pin- und Timerwerte ist der oben beschriebene Repository-Stand.

<details>
<summary>Originaldokumentation und Autorenhinweise anzeigen</summary>

//                                             - - o o - -                                       //
//                                         This file belongs to:                                 //
//// //// ////     ESP8266 Project Timerclock by John Halfords, The Netherlands      //// //// ////
//                                             - - o o - -                                       //

# TimerClock_with_SunSet-SunRise
VERSION 3.1, Date: 18 November 2021

!!! Attention:
- Code written for ESP8266 (Core 2.7.4)
- Dusk2Dawn.h library download zip from Github! Don't install with Arduino IDE! (https://github.com/dmkishi/Dusk2Dawn)

Timer Clock program for the ESP8266.
4 timers with ON and OFF time or automatically switch ON and/or OFF by SunSet and/or SunRise with Offset.
The timers control each a user definable GPIO and a http-command.

Hardware: ESP8266 (I have used the Wemos NodeMcu V3 Lolin).

My hardware design is described in document: "TimerClock Project (ESP8266) Schematic.xlsx"
My Arduino IDE settings are in an screenshot called: "Arduino IDE setting.png"


 Functionality:
 ==============
 - Webserver at http://ip-adres/
 - Extended settings page where you can set Timezone, Longitude, Latitude and several GPIO controls
 - Update your sketch bij http (settings page)
 - Remote reset and restart (settings page)
 - The program controls 4 outputs, which are in my hardware design connected to an RF remote control,
   which controls my ligths in my house (controls page)
 - Every timer statuschange (ON or OFF) can also fire a http-command, you can use it for example for webhooks or Tasmota
 - On the website you can control the outputs manually (in my case: switch the ligths on/off)
 - On the website you can configure 4 timers (Timer a - d) (timers page)
   Every timer has it's own ON-time and OFF-time
   Per time you can choose if you set the time manually or set it to SunSet or SunRise (with up to 99 minutes offset)
   
 Used Libraries:
 ===============
 - ESP8266WiFiMulti.h          library to connect to WiFi and automaticly choose the strongest network
 - ESP8266WebServer.h          library to run the WebServer
 - ESP8266HTTPUpdateServer.h   library for updating sketch through http
 - EEPROM.h                    library to write to, and read from EEPROM
 - ezTime.h                    Very well documented Time libary with everything you possibly want! (https://github.com/ropg/ezTime)
 - Dusk2Dawn.h                 Library to define Sunset and Sunrise (https://github.com/dmkishi/Dusk2Dawn)
 - ESP8266HTTPClient.h         Library to run as a HTTP cliënt (to send the http-commands)

 
 Final Note, about me:
 =====================
 - I am not a programmer.
 - I do a lot of trial and error, googling and look at other people's code. That's how i learn.
 - I have tried to document the code as highly and clearly as possible.
 - I am Dutch, so my English is not flawless...
 - I really like to receive your comments or questions, thanks in advance.
 
 GrTx, John


</details>

