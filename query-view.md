# query-view

## multiple reccord show

## controller
````
$this->load->database();
$this->db->cache_on();
$data['user'] = $this->db->get('e_tb', 5, 6);
$this->load->view('view_centraladmin',$data);
````    
## view
````    
foreach ($user->result() as $row) {
  echo $row->euser;
}
````
## 1 reccord show
## controller
````
public function edit($id)
{
  $this->load->database();
  $this->db->cache_on();
  $this->db->select('eid, ename, euser');
  $data['useredit'] = $this->db->get_where('e_tb', array('eid' => $id));
  $this->load->view('view_centraladmin_edit',$data);
}
````
## view
````    
$row = $useredit->row();
echo $row->eid;
echo $row->euser;
echo $row->ename;
````
