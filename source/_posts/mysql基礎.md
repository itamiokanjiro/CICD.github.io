---
title: mysql基礎
date: 2025-06-02 13:00:14
tags:
---


PHP連接mysql

    $conn = new mysqli("localhost", "user", "pass");


    $sql = "CREATE DATABASE database1";
    $conn->query($sql)

    $conn = new mysqli("localhost", "user", "pass", "database1");
    $query = "select * from table_1";
    $result = $conn->query($query);



    $searchInput =  $_POST['findUser'];
    $query = "select * from users where name like '%$searchInput%'";
    $result = $conn->query($query);

    while($row = $result->fetch_assoc() ){
	echo $row["name"]."<br>";
    }

    
查版本

    SELECT VERSION();
