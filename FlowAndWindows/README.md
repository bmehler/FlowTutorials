VagrantT3Flow: Mit Notpad++ an Flow-Projekten unter Vagrant arbeiten
======================================================================

Mit diesem kleinen Tutorial m�chte ich kurz erkl�ren, wie man an einem Flow-Projekt, welches sich in Vagrant auf einem LAMP-Server befindet, von Windows aus mit Notepad++ (Npp) arbeiten kann.

Zun�chst gehe ich davon aus, dass ihr Vagrant und einen funktionierenden LAMP-Server installiert habt.
Wie ihr euch unter Debian "Wheezy" einen LAMP-Server aufbaut, k�nnt ihr diesem [Tutorial](http://www.howtoforge.com/installing-apache2-with-php5-and-mysql-support-on-debian-wheezy) entnehmen.

Nun zum Eigentlichen.

Letzte Woche wollte ich Flow auf Windows 7 Home Premium installieren und scheiterte kl�glich.
Nachdem ich mir den Flow-Source heruntergeladen und auf dem Webserver entpackt habe, bekam ich
beim ersten Aufruf den Fehler Warning: symlink(): Cannot create symlink, error code(1314).

Wie man den [Flow_Installation_Hints](http://wiki.typo3.org/Flow_Installation_Hints#Symbolic_Links) entnehmen kann, m�ssen die Berechtigungen zum Erstellen von Symlinks in Windows entsprechend angepasst werden.

Um das Ganze abzuk�rzen: da es in der Windows 7 Home Premium keine M�glichkeit gibt die erforderliche Berechtigung zu setzen (keine secpol.msc, gpedit.msc) stand ich vor folgendem Problem:

* Installiere ich Flow auf meinem Windows-Rechner ist dies wegen der fehlenden M�glichkeit automatisch symlinks zu generieren nicht m�glich. (nat�rlich kann man mit mklink manuell symbolische Links erzeugen)
* Installiere ich Flow auf meinem Lamp-Server in der Vagrant-Umgebung, habe ich zwar eine funktionierende Flow-Installation, jedoch nicht die M�glichkeit mit einer IDE zu arbeiten. Das Arbeiten mit nano oder der vim gleicht hierbei einem Schu� ins Bein.

Und Nun? Die Synchronisierung vom Host (Windows) auf den Guest(Vagrant) l�sst sich ja problemlos in der Vagrantfile vornehmen. Aber gibt es eine M�glichkeit Ordner vom Guest (Vagrant) auf den Host (Windows) zu synchronisieren, so dass ich mein Flow-Verzeichnis in Windows zur Verf�gung h�tte? 

Meine Recherchen ergaben, dass dies mit nfs m�glich w�re. In Linux ist dies laut Vagrant Dokumentation nur die  Installation eines einfachen Paketes. Wie aber in Windows? Dienste f�r NFS? Diese sollten unter Programme | Programme und Funktionen | Windows Funktionen aktivieren und deaktivieren verf�gbar sein. Bei mir leider nicht bzw. nicht ohne weitere �nderungen an meinem Windows. Demnach scheidet nfs als Option ein bequemes arbeiten zwischen Vagrant und Windows zu gew�hrleisten f�r mich aus.

Aber wie geht es weiter? Bei meinen weiteren Recherechen entdeckte ich, dass in Bezug auf Vagrant und Flow immer wieder PHPStorm und die einfach M�glichkeit auf die Vagrant-Verzeichnisse mittels FTP zuzugreifen erw�hnt wurde.
Da PHPStorm kostenpflichtig ist und auch Npp �ber eine FTP-Erweiterung (NppFTP) verf�gt, kam mir der Gedanke einen FTP-Client in Npp zu Vagrant herzustellen. Die n�tigen Einstellungen finden sich in der Bilddatei in diesem GitHub-Repository.

**Folgende Schritte bzw. Eingaben sind hierf�r notwendig:**

* Bei den **Profil-Settings** muss ein neues Profil angelegt werden. (z. B. der Name des Flow-Projektes - vagrantflow)
* Bei **Hostname** `127.0.0.1`, bei Connection Type `SFTP` w�hlen und bei Port `2222` eingeben.
* **Username** und **Password** ist jeweils `vagrant`
* Zuletzt den **Order** `(z.b. /var/www/vagrantflow)` den Ihr per FTP verbinden m�chtet.
 
Nachdem man nun mit `vagrant up` die Vagrant-Umgebung gestartet hat, �ffnet man in Npp die Erweiterung NppFTP und dr�ckt den Verbindungskopf. Der Verzeichnisbaum erscheint im rechten oberen Fenster. Nun kann man bequem am Flow-Projekt per FTP arbeiten und Aktualisierungen werden sofort mit dem verbundenen Order abgeglichen. Falls man beim Speichern eine Fehlermeldung erh�lt (z. B. Permissions failed), m�ssen im verbundenen Ordner die Verzeichnnisse bzw. Dateien z.b. mittels chmod freigegeben werden.

Vielleicht konnte ich dem einen oder anderen helfen. Viel Spa�!