FlowAndTypeHints: Program to an interface, not an implementation
================================================================
In diesem Tutorial möchte ich mich mit dem <i>Grundsatz Program to an interface, not an implementation</i> beschäftigen.
Seit geraumer Zeit beschäftige ich mich nun mit Flow bzw. dem in das TYPO3 CMS rückportierte Framework Extbase. Gestern stellte ich mir die Aufgabe den Persitenzmechanismus in Exbase Schritt für Schritt durchzugehen. Dies beginnt, wie die eingefleischten Extbase'ler von euch sicherlich wissen, mit dem Absetzen einer Query im <Model>Repository. Danach werden verschiedene Klassen aufgerufen wie z. B. die Query oder die QueryFactory. Schließlich kommt man zum PersistenceManager, welcher wie der Name schon sagt, das Objekt in die Datenbank schreibt bzw. es aus der Datenbank ließt.

Warum schreibe ich nun über <i>Program to an interface, not an implementation</i>? Kurzum in den oben aufgeführten Klassen fand ich immer wieder type hints welche das übergebene Objekt auf ein Inteface prüfen. So z. B.

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

Aber wie kann man ein Interface als type hint setzen. Ein Inteface besteht lediglich aus der Methodendeklaration, welche obligatorisch public ist und verfügt des weiteren über keinen Methodenrumpf. Also gegen was soll das Objekt denn geprüft werden? Schauen wir uns das QueryInterface doch mal genauer an!

```php
/**
 * A persistence query interface
 *
 * @api
 */
	interface QueryInterface {
...
/**
 * Executes the query and returns the result.
 *
 * @return \TYPO3\CMS\Extbase\Persistence\QueryResultInterface|array The query result object or an array if 		$this->getQuerySettings()->getReturnRawQueryResult() is TRUE
 * @api
 */
public function execute();
```
So enthält das QueryInterface z. B. die Funktion execute(). Wie unschwer zu erkennen ist, ist diese als public deklariert und verfügt über keinen Methodenrumpf. Aber wie nun weiter? Naja, das QueryInterface wird doch bestimmt an eine Klasse vererbt, oder? Aber an welche? Achja, an die Query-Klasse. Schauen wir uns diese gleich mal an.

```php
/**
 * The Query class used to run queries against the database
 *
 * @api
 */
class Query implements \TYPO3\CMS\Extbase\Persistence\QueryInterface {
...
/**
* Executes the query against the database and returns the result
*
* @return \TYPO3\CMS\Extbase\Persistence\QueryResultInterface|array The query result object or an array if $this->getQuerySettings()->getReturnRawQueryResult() is TRUE
* @api
*/
public function execute() {
	
	if ($this->getQuerySettings()->getReturnRawQueryResult() === TRUE) {
		return $this->persistenceManager->getObjectDataByQuery($this);
	} else {
		return $this->objectManager->get('TYPO3\\CMS\\Extbase\\Persistence\\QueryResultInterface', $this);
	}
}
```
Jetzt schließt sich der Kreis.

```php
<?php

interface ClothesInterface {
	
	public function getClothing();

}

abstract class Person implements ClothesInterface {

	public $additions;
	
	public function __construct($additions = NULL) {
	
		$this->additions = $additions;
	
	}
}

class Doctor extends Person implements ClothesInterface {	

	public function getClothing() {
		
		if ($this->additions  == 'stethoscope') {
			return 'A doktor wears a tunic and stethoscope.';
		} else {
			return 'He only wears a tunic.';
		}	
	}

}

class Consultant extends Person implements ClothesInterface {

	public function getClothing() {
		
		if ($this->additions  == 'tie') {
			return 'He is complete with a suit and tie!';
		} else {
			return 'He only wears a suit!';
		}
	}
}

class main {

	function getPerson(ClothesInterface $person){
	
		echo $person->getClothing();
	}
}

// A Doctor Object
echo 'The Doctor<br />';
$newDoctor = new Doctor('stethoscope');
$clothes = new main();
$clothes->getPerson($newDoctor);

// A Consultant Object
echo '<br />The Consultant</br />';
$newConsultant = new Consultant('tie');
$clothes = new main();
$clothes->getPerson($newConsultant);


?>
```
