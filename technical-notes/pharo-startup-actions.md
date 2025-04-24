# Configuring Pharo with startup action. 

You can define in your preference folder a file finishing by `.st`.
This will be automatically executed each time you start the system.
You can place SSH or HTTPS Github configurations

Here is a sample, it hides the logo to help you know if the actions have been executed. 
You can execute this file explicitly by using the System>run startup script menu item. 

```
StartupPreferencesLoader default executeAtomicItems: {
	StartupAction 
		name: 'Logo' 
		code: [ PolymorphSystemSettings showDesktopLogo: false] .
	StartupAction 
		name: 'Git Settings' 
		code: [ 
			Iceberg enableMetacelloIntegration: true.
			IceCredentialsProvider sshCredentials
					username: 'git';
					publicKey: '/Users/XXX/.ssh/id_rsa.pub';
					privateKey: '/Users/XX/.ssh/id_rsa'.
			IceCredentialStore current
					storeCredential: (IcePlaintextCredentials new
					username: 'GHUSER';
					password: 'PassWord';
					host: 'github.com';
					yourself).		


			IceCredentialStore current
				storeCredential: (IceTokenCredentials new
					username: 'GHUSER';
					token: 'YOUR TOKEN';
					yourself) 
				forHostname: 'github.com'.
			]. 
}.

```