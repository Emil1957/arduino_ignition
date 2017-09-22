
Projekt "Elektronische Zündung auf Arduino-Basis"
===================================================

Ich stelle hier die Software für mein Projekt "Elektronische Zündung auf Arduino-Basis". Das Programm (entwickelt mit der Arduino IDE) habe ich für meine Honda CB250K4 geschrieben (180° Gegenläufer-Twin), durch geringfügige Modifikationen kann es aber auch für Motorräder mit anderer Zündcharakteristik verwedet werden (eine Include-Datei für eine Ducati und einen 360° Gleichläufer-Twin bzw. Boxer ist enthalten).

Eine Steuerscheibe auf der Nockenwelle (die Geometrie hängt von der Zündcharakteristik des Motorrades ab) wird so justiert, dass zu dem Zeitpunkt, wenn der erste Zylinder den UT (vor Beginn des Verdichtungstaktes) erreicht, ein Hallsensor ein Signal an Pin 2 des Arduino sendet (bzw. der PIn, der INTERUPT0 zugeordnet ist). Dieser löst dann die Interrupt-Prozedur aus (der Interrupt ist auf den Modus CHANGE in konfiguriert). Darin wird dann über einen "digitalRead" ermittelt, ob das Signal <> LOW ist (Steuerscheibe deckt Hallsensor ab), dann ist nachfolgend Zündspule1 zu behandeln, andernfalls (Steuerscheibe hat Hall-Sensor wieder frei gegebn) ist es Zündspule2.

Für die Justierung der Steuerscheibe dienen zwei LEDs, die im Konfigurationsmodus aufleuchten, wenn die Steuerscheibe beginnt, den Hallsensor zu überdecken bzw. diesen wieder freizugeben. Der verwendete Hallsensor ist folgender: 
http://www.hallsensors.de/CYHME1AV.pdf. Es kann aber auch ein anderer "Hall efect vane sensor" verwendet werden.

Wenn die Interrup-Prozedur ausgeführt wird, wird ein Timer gestartet. Das "timerIntervall" ist dabei die Zeit, die bis zum Beginn der Ladezeit der Spule "gewartet" werden muss. Diese Zeit wird aus der aktuellen Umdrehungszeit der Nockenwelle berechnet (aus vorherigen Signalen des Hallsensors) und ist umso kürzer, je höher die Umdrehungszahl ist (bzw. je kleiner die Zeit für eine Umdrehung ist). 

Wenn der Timer "feuert" (der "Wecker klingelt") wird eine Timer-Interrupt-Routine aufgerufen. Dort wird der Timer gestopt und anschließend erneut gestartet, diesmal ist das "timerIntervall" die Ladezeit der Spule, die intern auf 1500 µs gesetzt ist. Gleichzeitig wird über "digitalWrite" der Pin, an dem die betreffende Spule hängt, auf HIGH gesetzt, was dazu führt, dass die Spule geladen wird.

Wenn der Timer erneut "feuert", ist der Zeitpunkt der Zündung erreicht. Über "digitalWrite" wird der "Spulenpin" auf LOW gesetzt, was dem Öffnen des Unterbrecherkontaktes bei einer Kontaktzündung entspricht. Das führt dann dazu, dass die betreffende Spule zündet.

Anschließend wartet das Programm darauf, dass die Interrupt-Prozedur für den Hall-Sensor erneut aufgerufen wird, dann wird der Vorgang für die andere Spule ausgeführt.

Alle Projekt-Dateien befinden sich in der Datei "Arduino_Zuendung.zip". Diese findet man in verschiedenen Unterordnern (die den jeweiligen "Projektstand" entsprechen). In jedem Unterordner findet man wieder eine Readme-Datei, die die Detailänderungen der jeweiligen Version beschreibt.

In der Zip-Datei ist auch ein Leiterplattenentwurf (als Fritzing-Datei) enthalten. Ich muss allerdings gestehen, dass ich Elektronik-Amatuer bin und das Layout professionellen Ansprüchen möglicherweise nicht genügt. Eine professionelle Lösung (mit KiCAD entwickelt) findet man im Ordner "Hardware".

Für den Programmtest habe ich folgende Testequipment verwendet:

-Soundkarten-Oszilloskop-Programm (https://www.zeitnitz.eu/scms/scope?mid=2)

-PC mit Soundkarte mit Line-In-Eingang (Notebook mit Mikrofon-Eingang funktioniert nach meiner Erfahrung nicht!)

-Selbstgeschriebenes Programm "VirtualEngine.exe", das verschiedene Rechecksignale für den Kopfhörer/Lautsprecher-Ausgang der Soundkarte erzeugt (die Datei VirtualEngine.zip enthält dieses Programm).
Die Signalstärke ist allerdings zum Ansteuern des Arduino-Einganges zu gering, ich habe mir ein kleines Mikrofonverstärker-Modul besorgt (gibts bei EBay für wenige Euro), damit funktioniert es.

-Im Arduino wird dann auf Basis des Eingangssignals ein Ausgangssignal berechnet (das normalerweise auf die Zündungs-Endstufe gegeben wird). Für Testzwecke kann das wieder in den Line-In-Eingang der Soundkarte eingespeist werden, dazu muss allerdings das 5V-Signal des Arduinos wieder auf max. 2V runtergeregelt werden, andernfalls kann die Soundkarte zerstört werden! Ein passender Spannungsteiler ist hier beschrieben: http://www.darc.de/fileadmin/_migrated/content_uploads/Messen_mit_dem_Oszilloskop2011.pdf
