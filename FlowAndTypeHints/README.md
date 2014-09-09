FlowAndTypeHints: Program to an interface, not an implementation
================================================================
In diesem Tutorial möchte ich mich mit dem <i>Grundsatz Program to an interface, not an implementation</i> beschäftigen.

Seit geraumer Zeit beschäftige ich mich nun mit Flow bzw. dem in das TYPO3 CMS rückportierte Framework Extbase. Gestern stellte ich mir die Aufgabe den Persitenzmechanismus in Exbase Schritt für Schritt durchzugehen. Dies beginnt, wie die eingefleischten Extbase'ler von euch sicherlich wissen, mit dem Absetzen einer Query im <Model>Repository. Danach werden verschiedene Klassen aufgerufen wie z. B. die Query oder die QueryFactory [(siehe Flow Doku zum genauen Query Mechanismus)](http://docs.typo3.org/flow/TYPO3FlowDocumentation/stable/TheDefinitiveGuide/PartIII/Persistence.html). Schließlich kommt man zum PersistenceManager der das Objekt in die Datenbank schreibt bzw. dieses wieder aus der Datenbank ließt. Zumeist gibt es zur Stammklasse auch ein Interface wie z. B. Query und QueryInterface.

Warum schreibe ich nun über <i>Program to an interface, not an implementation</i>? Kurzum: in den oben aufgeführten Klassen fand ich immer wieder type hints welche das übergebene Objekt auf ein Interface hin überprüfen. So z. B.

```php
/**
 * Returns the object data matching the $query.
 *
 * @param \TYPO3\CMS\Extbase\Persistence\QueryInterface $query
 * @return array
 * @api
 */
public function getObjectDataByQuery(\TYPO3\CMS\Extbase\Persistence\QueryInterface $query) {
	return $this->backend->getObjectDataByQuery($query);
}

```

Wie ihr sehen könnt wird das Object $query der Query Klasse gegen den Type Hint QueryInterface geprüft.
Aber wie kann man ein Interface als Type Hint setzen? Ein Interface besteht lediglich aus der Methodendeklaration, welche obligatorisch public ist und verfügt des weiteren über keinen Methodenrumpf. Also gegen was soll das Objekt denn geprüft werden? Die QueryInterface Klasse sieht wie folgt aus.

```php
/**
 * A persistence query interface
 *
 * @api
 */
interface QueryInterface {

	public function Methode();

}

```
Aber wie nun weiter? Naja, das QueryInterface wird doch bestimmt an eine Klasse vererbt, oder? Aber an welche? Achja, an die Query-Klasse.

```php
/**
 * The Query class used to run queries against the database
 *
 * @api
 */
class Query implements \TYPO3\CMS\Extbase\Persistence\QueryInterface {
	
	/**
	 * Hier die Implementierung der Methode 
	 */
	public function Methode() {
		if (...) {
			Anweisung;
		} else {
			Anweisung;
		}
	}
}
```
Jetzt schließt sich der Kreis. Die eigentliche Klasse Query tritt bei der Prüfung auf den Type Hint in den Hintergrund. So dient ein Interface zu deutsch Schnittstelle nicht nur dazu, dass sie Klassen vorschreibt welche Methoden diese beinhalten müssen, sondern muss vielmehr als Eingang in eine Vererbungshierachie gesehen werden. Dies hat den Vorteil, dass nur gegen die Schnittstelle und nicht gegen die Implementierung, welche ja jedesmal anders aussehen kann, geprüft wird.

Aber nun zu meinem Bespiel an welchem ich die Programmierung gegen ein Interface erklären möchte. Wie unschwer zu erkennen ist beginnt alles mit der Definition des ClothesInterface. Danach folgt eine abstrakte Klasse Personen, welche lediglich die Property $additions und den Konstruktor für die beiden Klassen beinhaltet. Da das ClothesInterface mit implements an die abstrakte Klasse Person vererbt wird und sowohl die Doctor als auch die Consultant Klasse von dieser erbt, besteht auch in diesen Klassen eine Verbindung zum ClothesInterface. Die Prüfung des $person Objektes, in unserem Fall ein Objekt der Klasse Doctor oder Consultant, geschieht in der Klasse main. Die Klasse prüft nur gegen das ClothesInterface und nicht gegen eine spezielle Klasse. Es kann somit festgehalten werden, dass die Prüfung gegen einen Interface Type Hint mehr Flexibiltät bietet, da die Implementierung diesen einfach nicht interessiert. 

So schützt die Trennung zwischen Schnittstelle und Implementierung den Anwender vor Implementierungsdetails und die Implementierung kann geändert bzw. ausgetauscht werden ohne das der Anwender davon betroffen ist.

```php
<?php

interface ClothesInterface
{
    public function getClothing();
}

abstract class Person implements ClothesInterface
{
    public $additions;

    public function __construct($additions = NULL)
    {
        $this->additions = $additions;	
    }	
}

class Doctor extends Person
{
    public function getClothing()
    {
        if ($this->additions  == 'stethoscope') {
            return 'A doktor wears a tunic and stethoscope.';
        } else {
            return 'He only wears a tunic.';
        }	
    }
}

class Consultant extends Person
{
    public function getClothing()
    {		
        if ($this->additions  == 'tie') {
            return 'He is complete with a suit and tie!';
        } else {
            return 'He only wears a suit!';
        }
    }
}

class main
{
    function getPerson(ClothesInterface $person)
    {		
        if ($person instanceOf Doctor || $person instanceOf Consultant) {
            echo $person->getClothing();	
        } else {
            echo 'Das Objekt ist weder eine Instanz der Klasse Doctor';
            echo 'noch der Klasse Consultant!';
        }
    }
}

echo 'The Doctor<br />';
$newDoctor = new Doctor('stethoscope');
$person = new main();
$person->getPerson($newDoctor);

echo '<br />The Consultant</br />';
$newConsultant = new Consultant('tie');
$person = new main();
$person->getPerson($newConsultant);
?>

```

Abschließend gibt es zu sagen, dass in unserem TYPO3-Beipiel zu Beginn die explizite Ausarbeitung der Methoden der Query-Klasse geändert bzw. ausgetauscht werden können. Das Object aber immer gegen den Inteface Type Hint geprüft wird und so eine Trennung zwischen Sturktur und Implementierung umgesetzt wird.

Für eure Aufmerksamkeit danke ich euch und würde mich freuen, wenn ich dem einen oder anderen mit meinen Ausführungen helfen konnte! Natürlich stehe ich Verbesserungen offen gegenüber.
