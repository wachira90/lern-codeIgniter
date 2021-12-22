# Creating Libraries

## load on controller
````
$this->load->library('check');
````

## use case in view or controller 
````
if($this->check->checknum($num)){
  return true;
}

if($this->check->checkdate($date)){
  return true;
}

````

## file \application\libraries\Check.php

````
<?php
defined('BASEPATH') or exit('No direct script access allowed');

class Check
{
	public function checkdate($date) //2021-12-19
	{
		if (preg_match("/^[0-9]{4}-[0-9]{2}-[0-9]{2}$/", $date)) {
			return true;
		} else {
			return false;
		}
	}

	public function checknum($num) //check number
	{
		if (is_numeric($num)) {
			return true;
		} else {
			return false;
		}
	}
````
