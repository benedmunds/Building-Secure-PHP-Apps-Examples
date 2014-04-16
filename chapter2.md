#Chapter 2
Examples of Apache and NGINX SSL configs


##Apache VHost

	<VirtualHost *:443>
	  DocumentRoot "/path/to/your/app/htdocs"
	  ServerName yourApp.com
	  SSLEngine on
	  SSLCertificateFile /usr/bin/ssl/yourAppSigned.crt
	  SSLCertificateKeyFile /usr/bin/ssl/yourApp.key
	</VirtualHost>


##NGINX VHost

	server {

		listen   443;

		server_name yourApp.com;
		location / {
			root   /path/to/your/app/htdocs;
			index  index.php;
		}

		ssl    				  on;
		ssl_certificate 	  /usr/bin/ssl/yourAppSigned.crt
		ssl_certificate_key   /usr/bin/ssl/yourApp.key

	}