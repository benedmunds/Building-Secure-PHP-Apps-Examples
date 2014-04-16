#Chapter 1
A basic example of prepared statements with typecasting.

	//database config vars
	//this would likely be in a config file somewhere
	$dbDsn      = 'mysql:host=localhost;dbname=buildsecurephpapps';
	$dbUsername = 'root';
	$dbPassword = 'root';

	//connect to the DB
	$db = new PDO($dbDsn, $dbUsername, $dbPassword);


	//the $id would probably come from a URL or request var
	$id = (int) 1001;

	//prep the query
	$query = $db->prepare('UPDATE users
	                       SET first_name = :first_name
	                       WHERE id = :id');
	//run it!
	$query->execute([
	  ':id'         => $id, //we know its an int already
	  ':first_name' => (string) $_POST['first_name'],
	]);