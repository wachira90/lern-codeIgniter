
# init config codeIgniter3


## autoload.php
````

$config['sess_save_path'] = sys_get_temp_dir();

$autoload['libraries'] = array('database', 'email', 'session', 'upload');

$autoload['drivers'] = array('cache');

$autoload['helper'] = array('url', 'file', 'form','security');

````

## config.php
````
$config['base_url'] = 'https://www.example.com/';

$config['log_file_permissions'] = 0775;

$config['cache_path'] = '/cache';

$config['encryption_key'] = 'xxxxxxxxxx';

$config['sess_save_path'] = sys_get_temp_dir();


$config['global_xss_filtering'] = FALSE;


$config['csrf_protection'] = TRUE;

$config['csrf_regenerate'] = TRUE;

````



