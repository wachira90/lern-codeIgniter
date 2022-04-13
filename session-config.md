
### application\config\config.php
````
$config['sess_driver'] = 'files';
$config['sess_cookie_name'] = 'ci_session';
$config['sess_expiration'] = 7200;
$config['sess_save_path'] = BASEPATH . 'session';
$config['sess_match_ip'] = FALSE;
$config['sess_time_to_update'] = 300;
$config['sess_regenerate_destroy'] = FALSE;
````

### secure 

````
// Set the cookie
setcookie(
    $this->sess_cookie_name,
    $cookie_data,
    $expire,
    $this->cookie_path,
    $this->cookie_domain,
    $this->cookie_secure,
    $this->cookie_httponly
);

//$config['cookie_httponly'] = TRUE;
````



````

$this->load->helper('cookie');

set_cookie($name[, $value = ''[, $expire = ''[, $domain = ''[, $path = '/'[, $prefix = ''[, $secure = NULL[, $httponly = NULL]]]]]]])

delete_cookie('name');

delete_cookie($name, $domain, $path, $prefix);

````
