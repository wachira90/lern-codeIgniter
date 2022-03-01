# Add PHPexcel Codeignitor

download PHPExcel Library and put into libraries folder.


## libraries  Excel.php

````
<?php
if (!defined('BASEPATH')) exit('No direct script access allowed');
require_once('PHPExcel.php');

class Excel extends PHPExcel
{
 public function __construct()
 {
  parent::__construct();
 }
}

?>
````

## Controllers - Excel_import_model.php

````
<?php if ( ! defined('BASEPATH')) exit('No direct script access allowed');

class Excel_import extends CI_Controller
{
 public function __construct()
 {
  parent::__construct();
  $this->load->model('excel_import_model');
  $this->load->library('excel');
 }

 function index()
 {
  $this->load->view('excel_import');
 }
 
 function fetch()
 {
  $data = $this->excel_import_model->select();
  $output = '
  <h3 align="center">Total Data - '.$data->num_rows().'</h3>
  <table class="table table-striped table-bordered">
   <tr>
    <th>Customer Name</th>
    <th>Address</th>
    <th>City</th>
    <th>Postal Code</th>
    <th>Country</th>
   </tr>
  ';
  foreach($data->result() as $row)
  {
   $output .= '
   <tr>
    <td>'.$row->CustomerName.'</td>
    <td>'.$row->Address.'</td>
    <td>'.$row->City.'</td>
    <td>'.$row->PostalCode.'</td>
    <td>'.$row->Country.'</td>
   </tr>
   ';
  }
  $output .= '</table>';
  echo $output;
 }

 function import()
 {
  if(isset($_FILES["file"]["name"]))
  {
   $path = $_FILES["file"]["tmp_name"];
   $object = PHPExcel_IOFactory::load($path);
   foreach($object->getWorksheetIterator() as $worksheet)
   {
    $highestRow = $worksheet->getHighestRow();
    $highestColumn = $worksheet->getHighestColumn();
    for($row=2; $row<=$highestRow; $row++)
    {
     $customer_name = $worksheet->getCellByColumnAndRow(0, $row)->getValue();
     $address = $worksheet->getCellByColumnAndRow(1, $row)->getValue();
     $city = $worksheet->getCellByColumnAndRow(2, $row)->getValue();
     $postal_code = $worksheet->getCellByColumnAndRow(3, $row)->getValue();
     $country = $worksheet->getCellByColumnAndRow(4, $row)->getValue();
     $data[] = array(
      'CustomerName'  => $customer_name,
      'Address'   => $address,
      'City'    => $city,
      'PostalCode'  => $postal_code,
      'Country'   => $country
     );
    }
   }
   $this->excel_import_model->insert($data);
   echo 'Data Imported successfully';
  } 
 }
}

?>
````

## Models - Excel_import_model.php

````
<?php
class Excel_import_model extends CI_Model
{
 function select()
 {
  $this->db->order_by('CustomerID', 'DESC');
  $query = $this->db->get('tbl_customer');
  return $query;
 }

 function insert($data)
 {
  $this->db->insert_batch('tbl_customer', $data);
 }
}
````

## View Folder  excel_import.php

````
<!DOCTYPE html>
<html>
<head>
 <title>How to Import Excel Data into Mysql in Codeigniter</title>
 <link rel="stylesheet" href="<?php echo base_url(); ?>asset/bootstrap.min.css" />
 <script src="<?php echo base_url(); ?>asset/jquery.min.js"></script>
</head>

<body>
 <div class="container">
  <br />
  <h3 align="center">How to Import Excel Data into Mysql in Codeigniter</h3>
  <form method="post" id="import_form" enctype="multipart/form-data">
   <p><label>Select Excel File</label>
   <input type="file" name="file" id="file" required accept=".xls, .xlsx" /></p>
   <br />
   <input type="submit" name="import" value="Import" class="btn btn-info" />
  </form>
  <br />
  <div class="table-responsive" id="customer_data">

  </div>
 </div>
</body>
</html>

<script>
$(document).ready(function(){

 load_data();

 function load_data()
 {
  $.ajax({
   url:"<?php echo base_url(); ?>excel_import/fetch",
   method:"POST",
   success:function(data){
    $('#customer_data').html(data);
   }
  })
 }

 $('#import_form').on('submit', function(event){
  event.preventDefault();
  $.ajax({
   url:"<?php echo base_url(); ?>excel_import/import",
   method:"POST",
   data:new FormData(this),
   contentType:false,
   cache:false,
   processData:false,
   success:function(data){
    $('#file').val('');
    load_data();
    alert(data);
   }
  })
 });

});
</script>
````

