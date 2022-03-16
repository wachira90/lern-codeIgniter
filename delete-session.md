# delete old session

## production
````
public function clear_expire_session()
{
// $files = glob($this->config->config['sess_save_path'] . "*.txt");
$files = glob($this->config->config['sess_save_path'] . "/ci_session*");
// print_r( $files);
$now   = time();

	foreach ($files as $file) {
		if (is_file($file)) {
			if ($now - filemtime($file) >= $this->config->config['sess_expiration']) {
				unlink($file);
				// echo "|\n";
				// echo $file;
			}
		}
	}
	echo "success";
}
````

## original
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

# setting session path

````
$config['sess_save_path'] = sys_get_temp_dir();
$config['sess_save_path'] = BASEPATH . "sessions";
````

# show sess_save_path

````
echo $this->config->config['sess_save_path'];
````
