## insert
````
public function insert_entry()
{
        $this->title    = $_POST['title']; // please read the below note
        $this->content  = $_POST['content'];
        $this->date     = time();
        $this->db->insert('entries', $this);
}
````
## update

````
public function update_entry()
{
        $this->title    = $_POST['title'];
        $this->content  = $_POST['content'];
        $this->date     = time();
        $this->db->update('entries', $this, array('id' => $_POST['id']));
}
````
