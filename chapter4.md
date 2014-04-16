#Chapter 4
Examples of Authentication, Access Control, and Safe File Handing


##Authentication

###Login

	class UserModel
	{
	  //... other methods

	  function login($user, $password)
	  {
	    if (password_verify($_POST['password'], $user->pass) === TRUE) {
	      $valid = TRUE;
	    }

	    //other checks, error handling, etc...

	    if ($valid === TRUE) {
	      //successfully logged in

	      //just example session class
	      //your own code or framework will likely
	      //have it's own session wrapper
	      Session::put('user', $user->id);
	      return TRUE;
	    }

	    //failed to login
	    Session::put('user', NULL);
	    return FALSE;
	  }
	}


###Method for Querying the Current User

	class UserModel
	{
	  //... other methods

	  //... login method

	  function current()
	  {
	    $user = FALSE;

	    if (isset($_SESSION['user']) === TRUE) {
	      $user = DB::findById($_SESSION['user']);
	    }

	    return $user;
	  }

	  //let's imagine there is a method for retrieving
	  //the user's groups.
	}


###Verify the User has Appropriate Access

	class AdminController
	{
	  function __construct()
	  {
	    $userModel = new UserModel;
	    $user = $userModel->current();

	    //check to make sure the user is logged in and
	    //is a member of the admin group
	    if ($user === FALSE || in_array('admin', $user->groups) === FALSE) {

	      //please add a legit error message
	      //with headers and all that fancy stuff
	      die('Not Authorized');

	    }
	  }

	  //other admin only methods
	}



###Protected File Access

	Route::get(
	  '/accounting/statements/{year}/{month}',
	  function($year, $month) {

		//check user access
		$userModel = new UserModel;
		$user = $userModel->current();

		//check to make sure the user is logged in
		//and a member of accounting
		if ($user === FALSE || in_array('accounting', $user->groups) === FALSE) {
		  //please add a legit error message
		  die('Not Authorized');
		}

		//the user has access, let's read the file
		$directory = __DIR__ . '../uploads/acct/stmnts/';
		$filename  = (int) substr($year, 0, 4) . (int) substr($month, 0, 2) . '.pdf';

		//error handling for invalid files

		//open the file
		$fileHandle = fopen($directory . $filename, 'r');

		//read the file and close it
		$fileContents = fread($fileHandle);
		fclose($fileHandle);

		//send the appropriate headers
		header('Content-type: application/pdf');
		header('Content-Disposition: inline; filename="' . $filename . '"');
		header('Content-Length: ' . filesize($directory . $filename));
		header('Expires: 0');
		header('Cache-Control: must-revalidate');
		header('Pragma: public');

		//echo the file contents to the browser
		echo $fileContents;

	});