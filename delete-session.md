# delete old session

````
<?php
$files = glob($config['sess_save_path'] . "*");

$now   = time();

foreach ($files as $file) {
	if (is_file($file)) {
		if ($now - filemtime($file) >= $config['sess_expiration']) {
			unlink($file);
		}
	}
}
````

# other example

````
<?php
echo filemtime("webdictionary.txt");
echo "<br>";
echo "Content last changed: ".date("F d Y H:i:s.", filemtime("webdictionary.txt"));
?>
````
