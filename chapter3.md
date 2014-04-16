#Chapter 3
Example of generating a secure password hashes.


##< PHP 5.5

###Store Password Hashing

	//generate the binary random salt
	$saltLength = 22;
    $binarySalt = mcrypt_create_iv($saltLength, MCRYPT_DEV_URANDOM);

    //convert the binary salt into a safe string
    $salt = substr(strtr(base64_encode($binarySalt), '+', '.'), 0, $saltLength);


    //set the cost of the bcrypt hashing
	//remember to experiment on your server to find the right value
	$cost = 10;

	//now we'll combine the algorithm code ($2y$) with the cost and our salt
	$bcryptSalt = '$2y$' . $cost . '$' . $salt;

	//hash it, hash it good
	$passwordHash = crypt($_POST['password'], $bcryptSalt);

	//verify a secure hash was returned
	//this could be an error code or insecure hash
	if (strlen($passwordHash) === 60) {

	  //this next part is just for demonstration
	  $db = new ImaginaryDatabase;
	  $db->user()->create(array(
	    'password' => $passwordHash
	  ));

	}
	else {

	  //error handling

	}


###Login

	//we are going to start with a default state
	//of failure, always assume failure first
	$valid = FALSE;

	//grab the hash from our imaginary database
	$user = $db->user()-where(array(
	  'email' => $_POST['email']
	))->row();

	//now check to see if the login password matches
	$pass = $_POST['password'];
	if (crypt($pass, $user->pass) === TRUE) {
	  $valid = TRUE;
	}

	//other checks, error handling, etc...

	if ($valid === TRUE) {
	  //valid auth stuff
	}



##>= PHP 5.5

###Store Password Hashing

	//FYI - the default cost is 10, it can be customized though

	//hash it, hash it good
	$passwordHash = password_hash($_POST['password']);


	//this next part is just for demonstration
	$db = new ImaginaryDatabase;
	$db->user()->create(array(
	  'password' => $passwordHash
	));


###Login

	//we are going to start with a default state of failure
	//always assume failure first
	$valid = FALSE;

	//grab the password hash from our imaginary database
	$user = $db->user()-where([
	  'email' => $_POST['email']
	])->row();

	//now check to see if the login password matches
	$pass = $_POST['password'];
	if (password_verify($pass, $user->pass) === TRUE) {
	  $valid = TRUE;
	}

	//other checks, error handling, etc...

	if ($valid === TRUE) {
	  //valid auth stuff
	}