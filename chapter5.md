#Chapter 5
Examples of Authentication and Safe File Handing


##Dynamic Typing

###Strict Return Checking

	$phrase = 'I am the one who knocks';

	$letterExists = stripos($phrase, 'i');

	if ($letterExists !== FALSE) {

	  echo 'we should be here';
	  return TRUE;

	}


##CSRF

###Token Generation

	class Form
	{

		static function generateCsrf() {

		  $token = mcrypt_create_iv(
		    16,
		    MCRYPT_DEV_URANDOM
		  );

		  Session::flash(
		    'csrfToken',
		    $token
		  );


		  return $token;
		}

	}


###Generate Token for Form

	Route::get('/signup', function(){

	  $data['token'] = Form::generateCsrf();


	  return View::render('signup.form', $data);

	});



###Form View

	<form method="POST" action="/signup">

	  <label>
		First Name:
		<input type="text" name="first_name" />
	  </label>

	  <label>
		Last Name:
		<input type="text" name="last_name" />
	  </label>

	  <label>
		Email:
		<input type="text" name="email" />
	  </label>


	  <input type="hidden" name="token" value="<?=$token?>" />

	  <input type="submit" name="submit" value="Signup" />

	</form>


###POSTed Form Token Checking

	Route::post('/signup', function(){

	  //this would probably be abstracted away into
	  //a route filter or your form validation
	  if ($_POST['token'] === Session::get('csrfToken')) {

		//process the form

	  }


	  //like earlier, you should add a
	  //legit error message here
	  die('Invalid Form Data');

	});