# output

## json

````
// $query = $this->db->get('actiontb', 3, 4);
$query = $this->db->get('actionpictb');
return $this->output
  ->set_content_type('application/json')
  ->set_output(json_encode($query->result()));
````

````
$res = array('message' => 'success', 'files' => count($files));
return $this->output
  ->set_content_type('application/json', 'UTF-8')
  ->set_status_header(200)
  ->set_output(json_encode($res));
````  
