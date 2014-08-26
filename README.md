sai
===

form page : 
===========
<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    pageEncoding="ISO-8859-1" import="java.sql.*,java.util.*"%>
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
	var splitClass=classString.split(",");
	
	var splitStudent=stuString.split(",");

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
<%
Connection c = null;
try {
	Class.forName("com.mysql.jdbc.Driver");
	c = DriverManager.getConnection(
			"jdbc:mysql://localhost:3306/sai", "root", "whbs@123");
	Statement s = c.createStatement();
	ResultSet rs=s.executeQuery("select id,classes from classes");
	rs.last();
	int classcount=rs.getRow();
	rs.beforeFirst();
	
	String classString="";
	Map<Integer,String> classmap=new HashMap<Integer,String>();
	%>
	<tr>
	<th>Students/Classes</th>
	<%
	while(rs.next())
	{
		classString=classString+rs.getString(2)+",";
		classmap.put(rs.getInt(1), rs.getString(2));
		%>
		<th><%=rs.getString(2) %></th>
		<%
	}
	
	//System.out.println(classString);
	%>
	</tr>
	<%
	Statement s1 = c.createStatement();
	ResultSet rs1=s1.executeQuery("select id,students from atstudent");
	Map<Integer,String> stumap=new HashMap<Integer,String>();
	String stuString="";
	while(rs1.next())
	{
		stuString=stuString+rs1.getString(2)+",";
		stumap.put(rs1.getInt(1), rs1.getString(2));
	}
	
	//System.out.println(classmap+" "+stumap);
	rs1.last();
	int stucount=rs1.getRow();
	rs1.beforeFirst();
	
	for(int i=0;i<stumap.size();i++)
	{
		%>
		<tr>
		<td><%=stumap.get(i+1) %></td>
		<%
		for(int j=0;j<classmap.size();j++)
		{
			%>
			<td>
			<input type="text" id="<%=i%>_<%=j%>"/>
			</td>
			<%
		}
		%>
		</tr>
		<%
	if(stumap.size()==i+1)
	{
		%>
		<tr><td>total</td>
		<%
		for(int z=0;z<classmap.size();z++)
		{
			%>
			<td><input type="text" id="<%=stumap.size()%>_<%=z%>"/></td>
			<%
		}
		%>
		</tr>
		<%
	}
	} 
%>
<tr><td><input type="button" value="submit" onclick="clickMe('<%=stumap.size()%>','<%=classmap.size()%>','<%=classString%>','<%=stuString%>')"/></td></tr>

<%
}
catch(Exception e)
{
	
}

%>
</table>
<div>
<form action="saiInsert.jsp">
<table id="result_table">

</table>
</form>
</div>
</body>
</html>


=============================
Insert Code : 
-------------------


<%@page import="java.util.HashMap"%>
<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    pageEncoding="ISO-8859-1" import="java.sql.*,java.util.*"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
<title>Insert title here</title>
</head>
<body>
<%
Connection c = null;
try {
	Class.forName("com.mysql.jdbc.Driver");
	c = DriverManager.getConnection(
			"jdbc:mysql://localhost:3306/sai", "root", "whbs@123");
	Statement s = c.createStatement();
	ResultSet rs=s.executeQuery("select id,classes from classes");
	
	Map<Integer,String> classmap=new HashMap<Integer,String>();
	
	while(rs.next())
	{
		classmap.put(rs.getInt(1), rs.getString(2));
		
	}
	Statement s1 = c.createStatement();
	
	ResultSet rs1=s1.executeQuery("select id,students from atstudent");
	
	Map<Integer,String> stumap=new HashMap<Integer,String>();
	
	while(rs1.next())
	{
		stumap.put(rs1.getInt(1), rs1.getString(2));
	}
	
	Statement s4 = c.createStatement();
	
	int n4=s4.executeUpdate("truncate table attendence");
	
	for(int i=0;i<stumap.size();i++)
	{
		
		Statement s2 = c.createStatement();
		int n=s2.executeUpdate("insert into attendence(stu_id) values('"+stumap.get(i+1)+"')");
		for(int j=0;j<classmap.size();j++)
		{
			String requestData=request.getParameter(i+"_"+j);
			
			Statement s3 = c.createStatement();
			int n1=s3.executeUpdate("update attendence set "+classmap.get(j+1)+"='"+requestData+"' where stu_id='"+stumap.get(i+1)+"'");
		}
	}
	
	out.println("Inserted Successfully");
}
catch(Exception e)
{
	
}

%>
</body>
</h
