# Physical Computing
## Abgabe - Plant Music
##### Nach https://www.youtube.com/watch?v=yuJhKUunYPE&t=1s&ab_channel=Felbify oder Biodata Sonification von electricityforprogress 
----
 ## Vorhaben 
---
Mein Ziel war es, das Projekt so gut wie möglich umsetzen zu können. Der Aufbau und die Bestellung der Komponenten haben mir aufgrund der guten Dokumentation keine Schwierigkeiten bereitet. Um den Code auf den Arduino zu kompilieren, habe ich versucht, mit Platformio in Visual Studio Code zu arbeiten. Hierbei bin ich auf verschiedene Schwierigkeiten gestoßen und habe die genaue Fehlerursache nach mehreren Versuchen nicht aufheben können. Auf der Webseite von ElectricityforProgress wird ein ähnliches Projekt mit einem ESP erklärt. Das Projekt verwendet daneben noch weitere Komponenten, die ich nicht zur Verfügung hatte. Sodass ich versucht habe, das Problem mit Arduino IDE zu lösen. Ich habe den Zip Ordner des Projektes heruntergeladen und die Bibliotheken heruntergeladen und den Code kompiliert. Nachdem der Arduino auf Berührung der Pflanze reagiert hat, habe ich den Plant Music Synthezier an ein Audiointerface angeschlossen und verscuht die Daten in Ableton Live 11 aufzunehmen. Die Audiodatei stellt leider kein zufriedenstellendes Ergebnis dar, jedoch hat das Berühren der Pflanze Signale an Ableton senden können. Für die Zukunft würde ich gerne die Komponenten löten und die Auswertung der Midi Daten besser verwenden können.

![PlantMusicBild](https://user-images.githubusercontent.com/96259797/187294631-4dfe9e2f-7f96-4234-8972-a8871d819966.jpg)



## Benötigte Komponenten
---
- Breadboard
- Arduino Uno
- Jumper Wire
- CLIFF FT6320	Klinkenbuchse stereo, 3 pol, 3,5 mm	
- Kurzhubtaster B3F Serie, 1 NO, 24 V DC / 50 mA
- MABP 5S DIN-Buchse, 5-polig, halbrund, Printausführung
- Keramik-Kondensator, 500V, 47P
- Timer-IC, Typ 555, DIP-8
- IC-Sockel, 8-polig, doppelter Federkontakt
- Keramik-Kondensator, 100 nF, -20...+80 %, Y5V, 50/100 V, RM 5 (2x)
- Keramik-Kondensator 4,7 nF, 20 %, Z5U, 100 V, RM 2,54
- Elko, radial, 47µF, 25V, 105°C
- Drehpotentiometer, Mono, 10 kOhm, logarithmisch, 6 mm
- Klinkenbuchse stereo, 3 pol, 3,5 mm
- Steckernetzteil, 12 W, 5 V, 2,4 A
- Widerstände ( 7x "220 Ohm"; "100 Ohm"; "47 uF")
- Midi Kabel zu USB

## Das Projekt 
-----

![projektvonoben](https://user-images.githubusercontent.com/96259797/187295450-306985b2-3250-4405-9b18-67aa17331bcd.jpg)


Das Projekt realisiert einen Syntheziser, der die Übertragung von Leitfähigkeitsänderungen zwischen zwei Sonden unter Verwendung von MIDI-Daten zur Klangerzeugung generiert. Konkret werden die Sonden an eine Pflanze angeschlossen, die ihre Leitfähigkeitsänderungen als Midi Daten interpretiert werden.

Neben dem Arduino Uno kann ein kompartible Mikrokontroller für das Projekt verwendet werden. Es gibt 5 digitale Pins, die für PWM (Pulsweitenmodulation) verwendet werden, um die LEDs zu steueren, zwei analoge Eingänge (A0, A1), die zum Lesen des Potentiometers und der Taste verwendet werden, ein digitaler Interrupt-Eingang (INT0) an Pin 2, um den 555-Timer-Ausgang zu lesen, und ein serieller TX-Ausgang für MIDI-Daten.

Die seriellen MIDI-Daten werden mit 31250 Bit pro Sekunde vom TX-Pin des Arduino an die MIDI-DIN-Ausgangsbuchse auf dem Breadboard ausgegeben.  An diesen Port können Synthesizer, Computer und andere Geräte angeschlossen werden.  Auch USB kann verwendet werden, um die MIDI-Daten mit SerialMIDI in den Computer zu übertragen.

Durch Drücken der Taste wird der Menümodus aktiviert.  Eine LED pulsiert und zeigt das aktuelle Menü an. 
Durch Drehen des Potentiometers wird das Menü geändert und durch die LEDs angezeigt. 
Durch erneutes Drücken der Taste wird das Menü ausgewählt (Schwellenwert, Notenskala, MIDI-Kanal, Helligkeit) und der Werteauswahlmodus aufgerufen.
Nach 15 Sekunden, wenn das Potentiometer nicht gedreht oder die Taste nicht erneut gedrückt wurde, verlässt das Programm den Menümodus und setzt die normale Lichtshow fort. Drücken Sie die Taste innerhalb eines Menüs, um den Wert zu speichern.
Der Menümodus wird beendet, und das Programm kehrt zur normalen Lichtshow zurück.

Quelle: https://github.com/electricityforprogress/BiodataSonificationBreadboardKit

## Schaltplan 
---
<img width="792" alt="Schaltkreis@2x" src="https://user-images.githubusercontent.com/96259797/187295077-b1430e0c-d18b-4dcf-8528-2ea7d7de7448.png">
![schaltung](https://user-images.githubusercontent.com/96259797/187295137-c0005826-559c-4dab-9b24-98e0db15ab8f.jpg)


Aufbau Plan als Schritt für Schritt Anleitung unter: https://github.com/electricityforprogress/BiodataSonificationBreadboardKit/blob/master/Biodata%20Sonification%20-%20Arduino%20Shield%20Breadboard%20Kit%20v02.pdf


## Code verstehen 
---

Nachdem der Ordner heruntergeladen wurde und der Code in Arduine IDE gestartet wurde, müssen folgende Bibliotheke heruntergeladen werden: 

- EEPROMex
- Bounce.2
- LEDFader

## EEPROMex
Der im Arduino verbaute EEPROM dient der dauerhaften Speicherung von Werten. Alle geänderten Parameter im Menü werden durch den Button aktiv und werden im EEPROM-Speicher abgelegt. Dadurch kann sich das System die Einstellungen nach dem Ausschalten merken.
Quelle: https://docs.arduino.cc/learn/built-in-libraries/eeprom


## Bounce.2 Bibliothek
In diesem Proejkt wurde ein Taster eingebaut, der zwischen 5 Modi schaltet. Taster haben jedoch die Eigenschaft, nicht direkt in den nächsten Zustand zu schalten, sondern neigen dazu, ein paar mal hin- und herzuschalten (bouncing).


![](https://starthardware.org/wp-content/uploads/2019/08/Arduino-Taster-Button-DigitalRead4-Bounce-Debounce-Entprellen3.png)
In der Grafik wird dieses Verhalten verdeutlicht.

Abhilfe schaffen entweder hardwareseitige Entprellschaltungen (debouncing) oder softwareseitige Lösungen. Eine softwareseitige Lösung für das Bouncing-Problem ist die bounce.h-Bibliothek. Sie ermöglicht es, die Prellzeit des Tasters zu ignorieren. 
Quelle: https://starthardware.org/arduino-und-taster/

## LEDFader
Diese Arduino-Bibliothek kann einzelne LEDs im Hintergrund einblenden, ohne Ihr Hauptprogramm zu blockieren. Vorliegend werden die LEDS anhand der Midi Daten eingeblendet.
Quelle: https://github.com/jgillick/arduino-LEDFader

## Midi Serial Code 
MIDI ist ein asynchrones serielles Datenprotokoll. Der Standard-MIDI-Anschluss ist ein 5-poliger Anschluss. Der Controller sendet Daten unter Verwendung einer asynchronen seriellen Kommunikation über diesen Anschluss, und das empfangende Soundmodul (z. B. Synthesizer, Sampler oder ein anderes Ausgabegerät) liest diese Daten und interpretiert die Befehle, um Sound zu erzeugen. 
Quelle: https://tigoe.github.io/SoundExamples/midi-serial.html


https://user-images.githubusercontent.com/96259797/187295526-9572c0af-da6e-4f25-81d6-6272b815c819.mov


https://user-images.githubusercontent.com/96259797/187295599-7e6f3490-0665-44b5-b596-c93367188f4a.mov


