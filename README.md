fORM  CODE :   ATTENDANCE.PHP
=============================
 <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
<title>Insert title here</title>
 <script src="js/jquery.js"></script>

 <script type="text/javascript">

var resultQuery="";
function clickMe(students,classes,classString,stuString)
{
	
	resultQuery="";
	$('#result_table').empty();
	var splitClass = classString.split(",");
	
	var splitStudent = stuString.split(",");

	for(var z=0;z<splitStudent.length-1;z++)
	{
		//alert(splitStudent.length-1+"_"+z);
	}
	resultQuery=resultQuery+'<tr><th>Students/Classes</th>';
	for(var i=0;i<splitClass.length-1;i++)
	{
		resultQuery=resultQuery+'<th>'+splitClass[i]+'</th>';
	}
	resultQuery=resultQuery+'</tr>';
	
	
	for(var j=0;j<splitStudent.length-1;j++)
	{
		resultQuery=resultQuery+'<tr><td>'+splitStudent[j]+'</td>';
		
		for(var z=0;z<splitClass.length-1;z++)
		{
			var totalId=splitStudent.length-1+"_"+z;
			
			var inputNames=j+"_"+z;
			
			resultQuery=resultQuery+'<td><input name='+inputNames+' type="text" value='+((parseInt($('#'+j+"_"+z).val())/parseInt($('#'+totalId).val()))*100)+'%></h5></td>';

		}
		
	}
	
	resultQuery=resultQuery+'<tr><td><input type="submit" value="submit"/></td></tr>';
	
	resultQuery=resultQuery+'</tr>';
	
	$('#result_table').append(resultQuery);
}

</script> 

</head>
<body>
<table>
<?php 
  $con = mysql_connect("localhost","root","whbs@123");
  $db = mysql_select_db("attendance", $con);
 
function classes($x)  
{  
   return($x);  
}  
 
function students($y)  
{  
   return($y);  
}

  $a = array();
  $a1 = array();
	$rs = mysql_query("select id,classes from classes");
	
	$classcount=mysql_num_rows($rs);

	$classString="";
      
	?>
	<tr>
	<th>Students/Classes</th>
	<?php
	while($row = mysql_fetch_array($rs))
	{
		  $classString = $classString . $row['classes'].",";
                  array_push($a , $row['classes']);
                  
                  $b = array_map("classes",$a);
		
		?>
		<th> <?php echo $row['classes'];  ?> </th>
		<?php
	}
	
      
	
	?>
	</tr>

	<?php
	
	$rs1 = mysql_query("select id,student_id from students");
	
	$stucount = mysql_num_rows($rs);

	$stuString = "";
	while($row1= mysql_fetch_array($rs1))
	{
		$stuString = $stuString . $row1['student_id'] . ",";
		// stumap.put(rs1.getInt(1), rs1.getString(2));
                 array_push($a1,$row1['student_id']);
                 $c = array_map("students",$a1);
		
	}


	
	for($i=0; $i<sizeof($c);$i++)
	{
		?>

		<tr>
		<td> <?php echo $c[$i] ?> </td>
		<?php

		for($j=0;$j<sizeof($b);$j++)
		{
			?>
			<td>
			<input type="text" id="<?php echo $i ?>_<?php echo $j ?>"/>
			</td>
			<?php
		}
		?>
		</tr>
		<?php
	if(sizeof($c) == $i+1)
	{
                
		?>
		<tr><td>total </td>
		<?php
		for($z=0;$z<sizeof($b);$z++)
		{
			?>

			<td> <input type="text" id="<?php echo sizeof($c) ?>_<?php echo $z; ?>"/> </td>
			<?php
		}
		?>

		</tr>
		<?php
	}

	} 

?>
<tr><td><input type="button" value="submit" onclick="clickMe('<?php echo sizeof($c) ?>','<?php echo sizeof($b) ;?>',
'<?php echo $classString; ?>','<?php echo $stuString; ?>')"/></td></tr>

</table>
<div>
<form action="attendance_entry.php" method="post">
<table id="result_table">

</table>
</form>
</div>
</body>
</html>

Insert Code : attendance_entry.php : 
===================================
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
<title>Insert title here</title>
</head>
<body>

<?php 
$con = mysql_connect("localhost","root","whbs@123");
$db = mysql_select_db("attendance", $con);
   
$a = array();
$a1= array();
	
	
function classes($x)  
{  
   return($x);  
}  
 
function students($y)  
{  
   return($y);  
}  

 $rs = mysql_query("select id,classes from classes");
	while($row = mysql_fetch_array($rs))
	{
                array_push($a,$row['classes']);
		$b = array_map("classes",$a);
		
	}
	

     $rs1=mysql_query("select id,student_id from students");
	while($row1 = mysql_fetch_array($rs1))
	{
		 array_push($a1 ,$row1['student_id']);
		 $c = array_map("students",$a1);
	}
	
	
	print_r($c);

	$truncate = mysql_query("truncate table attendance");
	
	for($i=0;$i<sizeof($c);$i++)
	{



		$n = mysql_query("insert into attendance(student_id) values ('".$c[$i]."')");
		for($j=0;$j<sizeof($b);$j++)
		{
			$requestData = $_POST[$i."_".$j];
			
			 $n1 = mysql_query("update attendance set ".$b[$j]."='".$requestData."' where student_id='".$c[$i]."'");
		}
	}
	
	echo "Inserted Successfully";
        


?>
</body>
</html>


DATA BASE : 
=============
CREATE TABLE IF NOT EXISTS `attendance` (
  `id` int(10) NOT NULL AUTO_INCREMENT,
  `student_id` varchar(50) NOT NULL,
  `class1` varchar(50) NOT NULL,
  `class2` varchar(50) NOT NULL,
  `class3` varchar(50) NOT NULL,
  `class4` varchar(50) NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB  DEFAULT CHARSET=latin1 AUTO_INCREMENT=7 ;



CREATE TABLE IF NOT EXISTS `classes` (
  `id` int(10) NOT NULL AUTO_INCREMENT,
  `classes` varchar(10) NOT NULL,
  `status` char(1) NOT NULL DEFAULT 'Y',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB  DEFAULT CHARSET=latin1 AUTO_INCREMENT=5 ;

--
-- Dumping data for table `classes`
--

INSERT INTO `classes` (`id`, `classes`, `status`) VALUES
(1, 'class1', 'Y'),
(2, 'class2', 'Y'),
(3, 'class3', 'Y'),
(4, 'class4', 'Y');


CREATE TABLE IF NOT EXISTS `students` (
  `id` int(10) NOT NULL AUTO_INCREMENT,
  `student_id` varchar(50) NOT NULL,
  `status` char(1) NOT NULL DEFAULT 'Y',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB  DEFAULT CHARSET=latin1 AUTO_INCREMENT=7 ;

--
-- Dumping data for table `students`
--

INSERT INTO `students` (`id`, `student_id`, `status`) VALUES
(1, 'student1', 'Y'),
(2, 'student2', 'Y'),
(3, 'student3', 'Y'),
(4, 'student4', 'Y'),
(5, 'student5', 'Y'),
(6, 'student6', 'Y');









