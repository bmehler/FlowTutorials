FlowAndTypeHints: Program to an interface, not an implementation
================================================================
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
