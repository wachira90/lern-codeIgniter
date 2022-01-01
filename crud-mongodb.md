# CRUD mongodb

## application\config\autoload.php

````
$autoload['libraries'] = array('form_validation');
$autoload['helper'] = array('url');
````

## application\config\Mongodb.php

````
<?php
defined('BASEPATH') OR exit('No direct script access allowed');

$config['host'] = 'localhost';
$config['port'] = 27017;
$config['username'] = '';
$config['password'] = '';
$config['authenticate'] = TRUE;
//$config['authenticate'] = FALSE;
````

## application\libraries\Mongodb.php

````
<?php
defined('BASEPATH') OR exit('No direct script access allowed');

class MongoDB {
	
	private $conn;
	
	function __construct() {
		$this->ci =& get_instance();
		$this->ci->load->config('mongodb');
		
		$host = $this->ci->config->item('host');
		$port = $this->ci->config->item('port');
		$username = $this->ci->config->item('username');
		$password = $this->ci->config->item('password');
		$authenticate = $this->ci->config->item('authenticate');
		
		try {
			if($authenticate === TRUE) {
				$this->ci->conn = new MongoDB\Driver\Manager('mongodb://' . $username . ':' . $password . '@' . $host. ':' . $port);
			} else {
				$this->ci->conn = new MongoDB\Driver\Manager('mongodb://' . $host. ':' . $port);
			}
		} catch(MongoDB\Driver\Exception\MongoConnectionException $ex) {
			show_error('Couldn\'t connect to mongodb: ' . $ex->getMessage(), 500);
		}
	}
	
	function getConn() {
		return $this->ci->conn;
	}
	
}

````

## application\models\Usermodel.php

````
<?php
defined('BASEPATH') OR exit('No direct script access allowed');

class UserModel extends CI_model {
	
	private $database = 'testdb';
	private $collection = 'testcollection';
	private $conn;
	
	function __construct() {
		parent::__construct();
		$this->load->library('mongodb');
		$this->conn = $this->mongodb->getConn();
	}
	
	function get_user_list() {
		try {
			$filter = [];
			$query = new MongoDB\Driver\Query($filter);
			$result = $this->conn->executeQuery($this->database.'.'.$this->collection, $query);
			return $result;
		} catch(MongoDB\Driver\Exception\RuntimeException $ex) {
			show_error('Error while fetching users: ' . $ex->getMessage(), 500);
		}
	}
	
	function get_user($_id) {
		try {
			$filter = ['_id' => new MongoDB\BSON\ObjectId($_id)];
			$query = new MongoDB\Driver\Query($filter);
			$result = $this->conn->executeQuery($this->database.'.'.$this->collection, $query);
			foreach($result as $user) {
				return $user;
			}
			return NULL;
		} catch(MongoDB\Driver\Exception\RuntimeException $ex) {
			show_error('Error while fetching user: ' . $ex->getMessage(), 500);
		}
	}
	
	function create_user($name, $email) {
		try {
			$user = array(
				'name' => $name,
				'email' => $email
			);
			$query = new MongoDB\Driver\BulkWrite();
			$query->insert($user);
			$result = $this->conn->executeBulkWrite($this->database.'.'.$this->collection, $query);
			if($result == 1) {
				return TRUE;
			}
			return FALSE;
		} catch(MongoDB\Driver\Exception\RuntimeException $ex) {
			show_error('Error while saving users: ' . $ex->getMessage(), 500);
		}
	}
	
	function update_user($_id, $name, $email) {
		try {
			$query = new MongoDB\Driver\BulkWrite();
			$query->update(['_id' => new MongoDB\BSON\ObjectId($_id)], ['$set' => array('name' => $name, 'email' => $email)]);
			$result = $this->conn->executeBulkWrite($this->database.'.'.$this->collection, $query);
			if($result == 1) {
				return TRUE;
			}
			return FALSE;
		} catch(MongoDB\Driver\Exception\RuntimeException $ex) {
			show_error('Error while updating users: ' . $ex->getMessage(), 500);
		}
	}
	
	function delete_user($_id) {
		try {
			$query = new MongoDB\Driver\BulkWrite();
			$query->delete(['_id' => new MongoDB\BSON\ObjectId($_id)]);
			$result = $this->conn->executeBulkWrite($this->database.'.'.$this->collection, $query);
			if($result == 1) {
				return TRUE;
			}
			return FALSE;
		} catch(MongoDB\Driver\Exception\RuntimeException $ex) {
			show_error('Error while deleting users: ' . $ex->getMessage(), 500);
		}
	}
}
````

## application\controllers\Usercontroller.php

````
<?php
defined('BASEPATH') OR exit('No direct script access allowed');

class UserController extends CI_Controller {
	function __construct() {
		parent::__construct();
		$this->load->model('usermodel');
	}
	 
	function index() {
		$data['users'] = $this->usermodel->get_user_list();
		$this->load->view('users', $data);
	}
	
	public function create() {
        if ($this->input->post('submit')) {
            $this->form_validation->set_rules('name', 'Full Name', 'trim|required');
            $this->form_validation->set_rules('email', 'Email Address', 'trim|valid_email|required');

            if ($this->form_validation->run() !== FALSE) {
                $result = $this->usermodel->create_user($this->input->post('name'), $this->input->post('email'));
				if($result === TRUE) {
					redirect('/');
				} else {
					$data['error'] = 'Error occurred during saving data';
					$this->load->view('user_create', $data);
				}
            } else {
				$data['error'] = 'Error occurred during saving data: all fields are required';
                $this->load->view('user_create', $data);
            }
        } else {
            $this->load->view('user_create');
        }
    }
	
	function update($_id) {
		if ($this->input->post('submit')) {
            $this->form_validation->set_rules('name', 'Full Name', 'trim|required');
            $this->form_validation->set_rules('email', 'Email Address', 'trim|valid_email|required');

            if ($this->form_validation->run() !== FALSE) {
                $result = $this->usermodel->update_user($_id, $this->input->post('name'), $this->input->post('email'));
                if($result === TRUE) {
					redirect('/');
				} else {
					$data['error'] = 'Error occurred during updating data';
					$this->load->view('user_update', $data);
				}
            } else {
				$data['error'] = 'error occurred during saving data: all fields are mandatory';
                $this->load->view('user_update', $data);
            }
        } else {
			$data['user'] = $this->usermodel->get_user($_id);
            $this->load->view('user_update', $data);
        }
	}
	
	function delete($_id) {
		if ($_id) {
            $this->usermodel->delete_user($_id);
        }
		redirect('/');
	}
	
}
````

## application\views\Users.php

````
<?php defined('BASEPATH') OR exit('No direct script access allowed'); ?>

<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="utf-8">
	<title>Codeigniter MongoDB Create Read Update Delete Example</title>
	<link rel="stylesheet" type="text/css" href="<?=base_url()?>assets/css/style.css"/>
</head>
<body>

<div>
	<h1>Codeigniter MongoDB Create Read Update Delete Example</h1>
	
	<div>
		<?php echo anchor('/usercontroller/create', 'Create User');?>
	</div>

	<div id="body">
		<?php
			if ($users) {
		?>
        <table class="datatable">
            <thead>
				<tr>
					<th>Name</th>
					<th>Email</th>
					<th>Acions</th>
                </tr>
            </thead>
			<tbody>
				<?php
					$i = 0;
					foreach ($users as $user) {
						$col_class = ($i % 2 == 0 ? 'odd_col' : 'even_col');
						$i++;
					?>
					<tr class="<?php echo $col_class; ?>">
						<td>
							<?php echo $user->name; ?>
						</td>
						<td>
							<?php echo $user->email; ?>
						</td>
						<td>
							<?php echo anchor('/usercontroller/update/' . $user->_id, 'Update'); ?>
							  
							<?php echo anchor('/usercontroller/delete/' . $user->_id, 'Delete', array('onclick' => "return confirm('Do you want delete this record')")); ?>
						</td>
					</tr>
					<?php
				}
				?>
			</tbody>
        </table>
    <?php
        } else {
            echo '<div style="color:red;"><p>No Record Found!</p></div>';
        }
    ?>
	</div>
</div>

</body>
</html>
````

## assets/css/style.css

````
table.datatable {
	width:100%;
	border: none;
	background:#fff;
}
table.datatable td.table_foot {
	border: none;
	background: #fff;
	text-align: center;
}
table.datatable tr.odd_col {
	background: none;
}
table.datatable tr.even_col {
	background: #ddd;
}
table.datatable td {
	font-size:10pt;
	padding:5px 10px;
	border-bottom:1px solid #ddd;
	text-align: left;
}
table.datatable th {
	text-align: left;
	font-size: 8pt;
	padding: 10px 10px 7px;   
	text-transform: uppercase;
	color: #fff;
	background:url('../img/table/head.gif') left -5px repeat-x;
	font-family: sans-serif;
}

````

## application/views/User_create.php

````

<?php
	defined('BASEPATH') OR exit('No direct script access allowed');
?>
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <title>Codeigniter MongoDB Create Read Update Delete Example</title>
    </head>
    <body>
        <div id="container">
            <h1>Codeigniter MongoDB Create Read Update Delete Example</h1>
			
			<div>
				<?php echo anchor('/usercontroller', 'Back to Users');?>
			</div>
			
            <div id="body">
                <?php
					if (isset($error)) {
						echo '<p style="color:red;">' . $error . '</p>';
					} else {
						echo validation_errors();
					}
                ?>

                <?php 
					$attributes = array('name' => 'form', 'id' => 'form');
					echo form_open($this->uri->uri_string(), $attributes);
                ?>

                <h5>Full Name</h5>
                <input type="text" name="name" value="<?php echo set_value('name');?>" size="50" />

                <h5>Email Address</h5>
                <input type="text" name="email" value="<?php echo set_value('email');?>" size="50" />

                <p><input type="submit" name="submit" value="Submit"/></p>
                
                <?php echo form_close(); ?>
            </div>
        </div>
    </body>
</html>
````

## application/views/User_update.php

````
<?php
	defined('BASEPATH') OR exit('No direct script access allowed');
?>
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <title>Codeigniter MongoDB Create Read Update Delete Example</title>
    </head>
    <body>
        <div id="container">
            <h1>Codeigniter MongoDB Create Read Update Delete Example</h1>
			
			<div>
				<?php echo anchor('/usercontroller', 'Back to Users');?>
			</div>
			
            <div id="body">
                <?php
					if (isset($error)) {
						echo '<p style="color:red;">' . $error . '</p>';
					} else {
						echo validation_errors();
					}
                ?>

                <?php 
					$attributes = array('name' => 'form', 'id' => 'form');
					echo form_open($this->uri->uri_string(), $attributes);
                ?>

                <h5>Full Name</h5>
                <input type="text" name="name" value="<?php echo isset($user)?$user->name:set_value('name'); ?>" size="50" />

                <h5>Email Address</h5>
                <input type="text" name="email" value="<?php echo isset($user)?$user->email:set_value('email'); ?>" size="50" />

                <p><input type="submit" name="submit" value="Submit"/></p>
                
                <?php echo form_close(); ?>
            </div>
        </div>
    </body>
</html>
````


## Finish
