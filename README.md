# PHP連接MySQL資料庫

macOS Monterey 12.6
XAMPP 8.1.6
檔案在XAMPP的路徑：htdocs/example/

## 連接

>.gitignore 忽略那些不該上傳的 Git 檔案


```php=
#conn.php
<?php

$server_name = 'localhost';
$username = 'huang2';
$password = '0000';
$db_name = 'huang2';

$conn = new mysqli($server_name, $username, $password, $db_name);

if($conn->connect_error) {
    die('資料庫連線錯誤：' . $conn->connect_error);
}

$conn->query('SET NAMES UTF8');
$conn->query('SET time_zone = "+8:00"');

?>
```

## 取值

```php=
#index.php

<?php
require_once('conn.php');

$result = $conn->query("select * from users;");
if(!$result){
  die($conn->error);
}

while ($row = $result->fetch_assoc()){

    echo "id:" . $row['cID'] . '<br>';
    echo "username:" . $row['cName'] . '<br>';
}

?>
```

![](https://i.imgur.com/VrLWGeP.png)


## 新增資料

新增一個 apple

```php=
#add.php
<?php
require_once('conn.php');

$result = $conn->query("insert into users(
    cName) values('apple')");
if(!$result) {  
    die($conn->error);
}
print_r($result);
?>
```
![](https://i.imgur.com/uhLluqQ.png)


## input 新增使用者

```php=
#index.php

<?php
require_once('conn.php');

$result = $conn->query("select * from users;");
if(!$result){
    die($conn->error);
}

while ($row = $result->fetch_assoc()){

    echo "id:" . $row['cID'] . '<br>';
    echo "username:" . $row['cName'] . '<br>';
}

?>
<h2>新增 user</h2>

<form method="POST" action="add.php">
    username: <input name="username"/>
    <input type="submit"/>
</form>
```
後面新增一個表單
action="add.php"是目標網頁

```php=
#add.php

<?php
require_once('conn.php');

if (empty($_POST['username'])){
    die('請輸入 username');
}
$username = $_POST['username'];

$sql = sprintf(
    "insert into users(cName) values('%s')",
    $username
);
//這是抓蟲用的
echo 'SQL:' .$sql . "<br>";

$result = $conn->query($sql);
if (!$result) {
    die($conn->error);
}

echo "新增成功！"
?>
```
sprintf 格式化
先到 index.php
新增 abc
![](https://i.imgur.com/gVMxBPP.jpg)

新增成功會跳到add.php=

![](https://i.imgur.com/ga78aLB.jpg)

再跳回 index.php
看一下資料庫內容
![](https://i.imgur.com/c3wM3is.jpg)

資料庫
![](https://i.imgur.com/LGkeiQy.jpg)


以上都要手動改網址返回
在底下加個a連結
會方便許多


![](https://i.imgur.com/9UReCpw.jpg)

或是自動跳轉

在 add.php 最後加上一行。

```php=
header("Location: index.php");
```


## 刪除資料

### 先加個刪除鍵

```php=
echo "<a href='delete.php?id=x'>刪除</a>";
echo "<br>";
```


```php=
#index.php

while ($row = $result->fetch_assoc()){
    echo "id:" . $row['cID'] . '<br>';
    echo "username:" . $row['cName'] . '<br>';
    echo "<a href='delete.php?cID=".$row['cID'] . "'>刪除</a>";
    echo "<br>";
}

```

### 新增 delete.php 檔案

```php=
<?php
require_once('conn.php');

if (empty($_GET['cID'])){
    die('刪除失敗，沒有咬到id');
}
$id = $_GET['cID'];

$sql = sprintf(
    "delete from users where cID = %d",
    $id
);
echo $sql;

$result = $conn->query($sql);
if (!$result) {
    die($conn->error);
}

if($conn->affected_rows >= 1) {
    echo "<br>";
    echo "成功刪除 " . $conn->affected_rows . " 筆資料。";
}else{
    echo "查無資料";
}

//header("Location: index.php");
?>

<br>
<a href="index.php">返回 index.php </a>


```

$conn->affected_rows 會顯示被刪除的筆數的數量



### 編輯資料

在 index.php 裡面再加入一個表單

```php=

#index.php

<?php
require_once('conn.php');

$result = $conn->query("select * from users;");
if(!$result){
    die($conn->error);
}

while ($row = $result->fetch_assoc()){
    echo "id:" . $row['cID'] . '<br>';
    echo "username:" . $row['cName'] . '<br>';
    echo "<a href='delete.php?cID=".$row['cID'] . "'>刪除</a>";
    echo "<br>";
}

?>
<h2>新增 user</h2>

<form method="POST" action="add.php">
    username: <input name="username"/>
    <input type="submit"/>
</form>

<h2>編輯資料</h2>
<form method="POST" action="update.php">
    id: <input name="cID"/>
    username: <input name="cName"/>
    <input type="submit"/>
</form>
```

新增 update.php

```php=

<?php
require_once('conn.php');

if (empty($_POST['cID']) || empty($_POST['cName'])){
    die('請輸入 id 與 username');
}
$id = $_POST['cID'];
$username = $_POST['cName'];

$sql = sprintf(
    "update users set cName='%s' where cID=%d",
    $username,
    $id
);

//錯誤訊息
$result = $conn->query($sql);
if (!$result) {
    die($conn->error);
}

echo "修改成功！";
// header("Location: index.php");
?>

<br>
<a href="index.php">返回 index.php </a>

```

