## POST 
````
if ($this->input->server('REQUEST_METHOD') == 'POST') {
  print_r ($this->session->userdata());
}
````
## GET 
````
if ($this->input->server('REQUEST_METHOD') == 'GET') {
  print_r ($this->session->userdata());
}
````
