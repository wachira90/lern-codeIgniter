
## url_suffix example

````
$config['url_suffix'] = '.html';
````

## Use `site_url()`

````
<!-- No need .html -->
<a href="<?php echo site_url('welcome/register'); ?>">Register</a>
<!-- <a href="http://domain/welcome/register.html">Register</a> -->
````

## Or `anchor()`

````
// No need .html
echo anchor('welcome/register', 'Register');
// <a href="http://domain/welcome/register.html">Register</a>
````

### Note : Dont forget to load URL helper with $this->load->helper('url');
