# Codeigniter-Rename-file-on-upload

## auto encrypt
````
$config['encrypt_name'] = TRUE;
````
## self encrypt
````
$config['encrypt_name'] = FALSE;
$new_name = time().$_FILES["userfiles"]['name'];
$config['file_name'] = $new_name;
````
