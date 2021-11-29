# CSRF protection


## add => application/config/config.php
````
$config['csrf_protection'] = TRUE;
$config['csrf_regenerate'] = TRUE;
````

## add in form

````
<input type="hidden" name="<?= $this->security->get_csrf_token_name(); ?>" value="<?= $this->security->get_csrf_hash(); ?>" />
````

## exclude path 
````
$config['csrf_exclude_uris'] = array('api/person/add');

````
## or
````
$config['csrf_exclude_uris'] = array(
        'api/record/[0-9]+',
        'api/title/[a-z]+'
);
````
