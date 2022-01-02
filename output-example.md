# output

## json

````
// $query = $this->db->get('actiontb', 3, 4);
$query = $this->db->get('actionpictb');
return $this->output->set_content_type('application/json')->set_output(json_encode($query->result()));
````
