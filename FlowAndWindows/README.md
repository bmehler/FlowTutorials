VagrantT3Flow: Mit Notpad++ an Flow-Projekten unter Vagrant arbeiten
======================================================================

Mit diesem kleinen Tutorial möchte ich kurz erklären, wie man an einem Flow-Projekt, welches sich in Vagrant auf einem LAMP-Server befindet, von Windows aus mit Notepad++ (Npp) arbeiten kann.

Zunächst gehe ich davon aus, dass ihr Vagrant und einen funktionierenden LAMP-Server installiert habt.
Wie ihr euch unter Debian "Wheezy" einen LAMP-Server aufbaut, könnt ihr diesem [Tutorial](http://www.howtoforge.com/installing-apache2-with-php5-and-mysql-support-on-debian-wheezy) entnehmen.

Nun zum Eigentlichen.

Letzte Woche wollte ich Flow auf Windows 7 Home Premium installieren und scheiterte kläglich.
Nachdem ich mir den Flow-Source heruntergeladen und auf dem Webserver entpackt habe, bekam ich
beim ersten Aufruf den Fehler Warning: symlink(): Cannot create symlink, error code(1314).

Wie man den [Flow_Installation_Hints](http://wiki.typo3.org/Flow_Installation_Hints#Symbolic_Links) entnehmen kann, müssen die Berechtigungen zum Erstellen von Symlinks in Windows entsprechend angepasst werden.

Um das Ganze abzukürzen: da es in der Windows 7 Home Premium keine Möglichkeit gibt die erforderliche Berechtigung zu setzen (keine secpol.msc, gpedit.msc) stand ich vor folgendem Problem:

* Installiere ich Flow auf meinem Windows-Rechner ist dies wegen der fehlenden Möglichkeit automatisch symlinks zu generieren nicht möglich. (natürlich kann man mit mklink manuell symbolische Links erzeugen)
* Installiere ich Flow auf meinem Lamp-Server in der Vagrant-Umgebung, habe ich zwar eine funktionierende Flow-Installation, jedoch nicht die Möglichkeit mit einer IDE zu arbeiten. Das Arbeiten mit nano oder der vim gleicht hierbei einem Schuß ins Bein.

Und Nun? Die Synchronisierung vom Host (Windows) auf den Guest(Vagrant) lässt sich ja problemlos in der Vagrantfile vornehmen. Aber gibt es eine Möglichkeit Ordner vom Guest (Vagrant) auf den Host (Windows) zu synchronisieren, so dass ich mein Flow-Verzeichnis in Windows zur Verfügung hätte? 

Meine Recherchen ergaben, dass dies mit nfs möglich wäre. In Linux ist dies laut Vagrant Dokumentation nur die  Installation eines einfachen Paketes. Wie aber in Windows? Dienste für NFS? Diese sollten unter Programme | Programme und Funktionen | Windows Funktionen aktivieren und deaktivieren verfügbar sein. Bei mir leider nicht bzw. nicht ohne weitere Änderungen an meinem Windows. Demnach scheidet nfs als Option ein bequemes arbeiten zwischen Vagrant und Windows zu gewährleisten für mich aus.

Aber wie geht es weiter? Bei meinen weiteren Recherechen entdeckte ich, dass in Bezug auf Vagrant und Flow immer wieder PHPStorm und die einfach Möglichkeit auf die Vagrant-Verzeichnisse mittels FTP zuzugreifen erwähnt wurde.
Da PHPStorm kostenpflichtig ist und auch Npp über eine FTP-Erweiterung (NppFTP) verfügt, kam mir der Gedanke einen FTP-Client in Npp zu Vagrant herzustellen. Die nötigen Einstellungen finden sich in der Bilddatei in diesem GitHub-Repository.

**Folgende Schritte bzw. Eingaben sind hierfür notwendig:**

* Bei den **Profil-Settings** muss ein neues Profil angelegt werden. (z. B. der Name des Flow-Projektes - vagrantflow)
* Bei **Hostname** `127.0.0.1`, bei Connection Type `SFTP` wählen und bei Port `2222` eingeben.
* **Username** und **Password** ist jeweils `vagrant`
* Zuletzt den **Order** `(z.b. /var/www/vagrantflow)` den Ihr per FTP verbinden möchtet.
 
Nachdem man nun mit `vagrant up` die Vagrant-Umgebung gestartet hat, öffnet man in Npp die Erweiterung NppFTP und drückt den Verbindungskopf. Der Verzeichnisbaum erscheint im rechten oberen Fenster. Nun kann man bequem am Flow-Projekt per FTP arbeiten und Aktualisierungen werden sofort mit dem verbundenen Order abgeglichen. Falls man beim Speichern eine Fehlermeldung erhält (z. B. Permissions failed), müssen im verbundenen Ordner die Verzeichnnisse bzw. Dateien z.b. mittels chmod freigegeben werden.

Vielleicht konnte ich dem einen oder anderen helfen. Viel Spaß!