#[php] 日积月累

合并教师姓名
```
/*-------------------------------------------------------------------------
合并教师姓名                                     
/*-----------------------------------------------------------------------*/

header('Content-Type: text/html; charset=utf-8');

try{

	$pdo = new PDO('mysql:host=10.100.1.80; dbname=weiyuyan; port=3306; charset=utf8','root','nbsswyy2012',array(PDO::ATTR_ERRMODE=>PDO::ERRMODE_EXCEPTION));
}catch(PDOException $e){
	echo '数据库连接失败'.$e->getMessage();
	exit;
}

try{
	$stmt = $pdo->prepare("select guid,username,realname,firstname,lastname from data_teacher_master");
	$stmt -> execute();
	//设置获取模式
	$stmt->setFetchMode(PDO::FETCH_ASSOC);
	$teacher = $stmt->fetchAll();
}catch(PDOException $e){
	echo 'SQL语句执行失败'.$e->getMessage();
	exit;
}

try{
	$pdo->beginTransaction();
	$stmt = $pdo->prepare("update data_teacher_master set realname=? where guid=?");
	foreach($teacher as $val){
		if(!empty($val['firstname']) && !empty($val['lastname'])){
			$stmt->execute(array($val['firstname'].' '.$val['lastname'], $val['guid']));
			echo $val['username']."老师姓名合并成功\n";
		}
	}
	$pdo->commit();
}catch(PDOException $e){
	$pdo->rollback();
	echo 'SQL语句执行失败'.$e->getMessage();
	exit;
}
```