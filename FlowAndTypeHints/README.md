FlowAndTypeHints: Program to an interface, not an implementation
================================================================
In diesem Tutorial möchte ich mich mit dem Grundsatz Program to an interface, not an implementation beschäftigen.
Seit geraumer Zeit beschäftige ich mich nun mit Flow bzw. dem in das TYPO3 CMS rückportierte Framework Extbase. Gestern stellte ich mir die Aufgabe den Persitenzmechanismus in Exbase Schritt für Schritt durchzugehen. Dies beginnt, wie die eingefleischten Extbaseler von euch sicherlich wissen, mit dem Absetzen einer Query im <Model>Repository. Danach werden verschiedene Klassen aufgerufen wie z. B. die Query oder die QueryFactory. Schließlich kommt man zum PersistenceManager, welcher wie der Name schon sagt, das Objekt in die Datenbank schreibt bzw. es aus der Datenbank ließt.

Warum schreibe ich nun über Program to an interface, not an implementation? Kurzum in den oben aufgeführten Klassen fand ich immer wieder type hints welche sich auf ein Interface beziehen. So z. B. Hier Beispiel rein.

Aber wie kann man gegen ein Interface einen type hint setzen. Ein Inteface hat doch lediglich die Methodendeklaration und kann weder insanziert noch besteht in der jeweiligen Methode ein Methodenrumpf.


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
			return 'He only wears a suit!';;
		}
	}
}

class main {

	function getAllClothes(ClothesInterface $person){
	
		echo $person->getClothing();
	}
}

// A Doctor Object
echo 'The Doctor<br />';
$newDoctor = new Doctor('stethoscope');
$clothes = new main();
$clothes->getAllClothes($newDoctor);

// A Consultant Object
echo '<br />The Consultant</br />';
$newConsultant = new Consultant('tie');
$clothes = new main();
$clothes->getAllClothes($newConsultant);


?>
```
