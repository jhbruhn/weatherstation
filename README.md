HGO Wetterstation Dokumentation
===============================

Vorwort
-------------------------------
Wir schreiben das Jahr 2015, ein weiterer Abi-Jahrgang ist kurz davor
endlich von der Last der Schule erlöst zu werden. Tatort ist das Seminarfach "Naturwissenschaft im Alltag".  Unter der Leitung Frau Eckmeyers ergab sich die Idee, die Relikte der
alten Wetterstation wiederzubeleben. Eine fachmännische Analyse ergab:

 * Die Wetterkamera ist (ziemlich sicher) auf Firmware-Level kaputt, sie reagiert nicht mehr auf IP Kommunikation, Anschaffung einer neuen Kamera passte zeitlich nicht
 * Trotz vergammelter Kabel funktionierten die Sensoren noch, allerdings war der Funk-Empfänger eine Wetter-Uhr mit undokumentiertem USB-Protokoll mit Treibern, die für Win2k gedacht waren
 * Neben Vor-Abi auch noch eine Wetterstation aufzubauen, kann man machen, es macht aber durchaus Arbeit

Material
-----------------------------

Für den Betrieb der Wetterstation wurden neue Sensoren von ELV angeschafft.
Auf dem Dach sollten sich folgende Sensoren befinden:
 * [ELV KS-300-4 Funk-Kombi-Wettersensor](http://www.elv.de/output/controller.aspx?cid=74&detail=10&detail2=13109)
   Der Funk-Kombi-Sensor misst die Außentemperatur, Luftfeuchtigkeit, Windgeschwindigkeit und Regenmenge
 * [ELV ASH2200 Funk-Außensensor](http://www.elv.de/output/controller.aspx?cid=74&detail=10&detail2=20564)
   Der extra Außensensor (plaziert in der kleinen Box auf dem Dach) liefert eine zweite Messung von Temperatur und Luftfeuchtigkeit

Die Außensensoren sind batteriebetrieben, falls irgendwann keine Daten mehr Empfangen werden, müssen an den Sensoren die Batterien getauscht werden.

An dem PC befindet sich folgende Peripherie
 * [ELV USB-WDE1 Wetterdatenempfänger](http://www.elv.de/-353.html)
   Dieser Empfänger ist per USB-Kabel an einen USB 2 Port angeschlossen

Im Unterordner `manuals` finden sich zu allen Sensoren und dem Empfänger die Gebrauchsanleitungen als PDF. Da kein Web-/FTP-Server installiert ist, müssten die Dokumente über sftp heruntergeladen werden.
Der USB-Empfänger meldet sich als serielles Gerät, die Baud-Rate ist auf `9600` gesetzt. Der Empfänger ist unter `/dev/ttyUSB0` zu finden.


Dieser Server
------------------------------

Der Server läuft, und das soll auch so bleiben... Änderungen sollten *nur von fähigen Leuten* durchgeführt werden, für die `Linux` kein Fremdwort ist. Falls dieses Projekt später von einem anderen Jahrgang wieder aufgenommen wird, sollten an diesem Server *nur* Oberstufenschüler, die in Informatik LKs absolut unterfordert sind, arbeiten. Falls gegen 2017 ein solcher Schüler vorhanden sein sollte, kann er gerne das System updaten. 
Es ist ein Ubuntu 14.04.2 LTS Server (64-bit), der bis zum Jahre 2017 mit Sicherheitsupdates versorgt wird.
Systemupdates werden wöchentlich eingespielt, das System startet monatlich 1x neu.

Unter Iserv ist der PC im alten Serverraum 89 eingetragen, der Hostname ist `wetterstation.hgo-ol.de`, die IP ist `10.16.89.42`.  Der Server ist über SSH auf Port 22 zu erreichen, Benutzername ist `wetterstation`. Das Passwort findet sich bei den analogen Unterlagen zur Wetterstation und/oder auf der ausgedruckten Version dieses Zettels. Falls das Passwort nicht mehr aufzufinden ist, die Festplatte ist nicht verschlüsselt und die Daten liegen in einer LVM Partition.


Das Programm
-------------------------------

Das Wetterstationsprogramm ist in [Go](https://golang.org/) geschrieben. Der Quellcode ist entweder auf [GitHub](https://github.com/HappyCarl/weatherstation/) oder im Ordner `~/weatherstation-source` zu finden. Der Code ist mehr oder weniger dokumentiert.
Das Programm liest die Daten von dem Empfänger und liest aus dem "LogView" Format die benötigten Daten aus. Eine Erklärung zu dem Format findet sich in der Bedienungsanleitung des USB-Empfängers. Die geparsten Daten werden über einen HTTP-Server als JSON zur Verfügung gestellt. Der HTTP-Server Port muss über den Iserv von `wetterstation.hgo.ol.de:xxxxx` nach `hgo-ol.de:xxxxx` weitergeleitet werden, um aus dem Internet ereichbar zu sein.

Der Regenmesser
-------------------------------

Die Wetterstation liefert die Regenwerte als "Anzahl der Wippenschläge". Messungen haben ergeben, dass ungefähr 5ml Wasser die Wippe umkippen lassen und der Zähler erhöhen. Im Programm werden die Wippenschläge mit Hilfe der Oberfläche des Auffangtrichters in mm Wassersäule umgerechnet. In einem Array werden die letzten Wippenschlagdifferenzen über 1h bzw 24h gespeichert und dann die Regenmenge errechnet. 

Anmerkung zum Thema "Funk"
--------------------------------

Wie in dieser Dokumentation schon ersichtlich geworden sein sollte, kommunizieren die Wettersensoren über ein [Funk](https://www.youtube.com/watch?v=mUsn880UWPQ)-Protokoll.

![](http://i.minus.com/iwmeWi3Dkfpcx.gif)
