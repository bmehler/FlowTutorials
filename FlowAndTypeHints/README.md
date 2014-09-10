FlowAndTypeHints: Program to an interface, not an implementation
================================================================
In diesem Tutorial möchte ich mich mit dem <i>Grundsatz "Program to an interface, not an implementation"</i> beschäftigen.

Seit geraumer Zeit beschäftige ich mich nun mit Flow bzw. dem in das TYPO3 CMS rückportierten Framework Extbase. Gestern stellte ich mir die Aufgabe den Persitenzmechanismus in Exbase Schritt für Schritt durchzugehen. Dieser beginnt, wie die eingefleischten Extbase'ler von euch sicherlich wissen, mit dem Absetzen einer Query im <Model>Repository. Danach werden verschiedene Klassen aufgerufen wie z. B. die Query oder die QueryFactory [(siehe Flow Doku zum genauen Query Mechanismus)](http://docs.typo3.org/flow/TYPO3FlowDocumentation/stable/TheDefinitiveGuide/PartIII/Persistence.html). Schließlich kommt man zum PersistenceManager, welcher das Objekt in die Datenbank schreibt bzw. dieses wieder aus der Datenbank liest. Zumeist gibt es zur Stammklasse auch ein Interface, wie z. B. Query und QueryInterface.

Warum schreibe ich nun über <i>"Program to an interface, not an implementation"</i>? Kurzum: in den oben aufgeführten Klassen fand ich immer wieder Type Hints, welche das übergebene Objekt auf ein Interface hin überprüfen. Das ganze sieht in Extbase dann so aus:

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

Wie ihr sehen könnt, wird das Objekt $query gegen das QueryInterface typisiert.
Aber warum setzt man ein Interface als Type Hint? Ein Interface besteht lediglich aus der Methodendeklaration, welche obligatorisch public ist und verfügt des Weiteren über keinen Methodenrumpf. Die QueryInterface Klasse sieht wie folgt aus:

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
Nun aber, wie geht es weiter? Das QueryInterface wird doch bestimmt in einer Klasse implementiert, oder? Aber in welcher? Sehen wir doch mal nach ob es zum QueryInterface auch eine Query Klasse gibt. Et voilà, und hier ist sie:

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
Jetzt schließt sich der Kreis. Die konkrete Klasse Query tritt bei der Prüfung auf den Type Hint in den Hintergrund. So dient ein Interface nicht nur dazu, dass es Klassen einen Bauplan mitgibt welche Methoden diese beinhalten müssen, sondern muss vielmehr als weitere Generalisierung gesehen werden. Dies hat den Vorteil, dass unabhängig von der konkreten Klasse, nur gegen das Interface geprüft wird. 

Aber nun zu meinem Bespiel, an welchem ich die Programmierung gegen ein Interface erklären möchte. Wie unschwer zu erkennen ist, beginnt alles mit der Definition des ClothesInterface. Danach folgt die abstrakte Klasse Personen, welche lediglich die Property $additions und den Konstruktor für die beiden Klassen beinhaltet. Da das ClothesInterface mit implements in der abstrakten Klasse Person implementiert wird, und sowohl die Doctor Klasse als auch die Consultant Klasse von dieser erbt, hat in diesen Klassen das ClothesInterface Gültigkeit. Der ClothesInterface Type Hint kommt schließlich in der Klasse main und hier insbesondere in der getPerson() Methode zur Anwendung. Dieser Methode wird ein Objekt $person übergeben, welches dann gegen das ClothesInterface geprüft wird. Die Klassen Doctor oder Consultant sind hierbei nicht mehr von Bedeutung. 

Dies lässt den Schluss zu, dass die Programmierung gegen Schnittstellen einen entscheidenen Flexibiltätsvorteil hat, da hierdurch eine Trennung zwischen Schnittstelle und Implementierung umgesetzt werden kann. Im engeren bedeutet dies, dass bei dem Interface Type Hint eine Generalisierung stattfindet, welche nun nicht mehr die Prüfung auf eine konkrete Klasse in den Vordergrund stellt. Würde man einen Object Type Hint verwenden, so wäre man an eine Konkrete Klasse mit ihren Methoden gebunden. Ferner schützt diese Trennung den Anwender vor Implementierungsdetails und die Implementierung kann geändert bzw. ausgetauscht werden, ohne das der Anwender davon betroffen ist.

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
            return 'He is complete! What a wonderful tie!';
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

Abschließend gibt es noch zu sagen, dass in unserem TYPO3-Beipiel zu Beginn die expliziten Implementierungen in der Query-Klasse geändert bzw. ausgetauscht werden können, ohne das hiervon die Struktur berührt wird. Das Object wird immer gegen den Inteface Type Hint geprüft. Dadurch kann eine Trennung zwischen Sturktur und Implementierung sichergestellt werden.

Für eure Aufmerksamkeit danke ich euch und würde mich freuen, wenn ich dem einen oder anderen mit meinen Ausführungen helfen konnte! Natürlich stehe ich Verbesserungen offen gegenüber.
