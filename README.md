play-diy-scripts
================

Openshift deployment scripts for Play! app to get it running on Openshift with DIY cartridge


Complete Play application [here](https://github.com/thomson256/playshifted).


Deploy Play framework application to Openshift
==============================================

*Tested with Play Framework 2.3.8  Java 8*
Have application name in openshift and locally as the same, so ${OPENSHIFT_APP_NAME} remain correct.


- Install Openshift [command line tools](https://www.openshift.com/developers/rhc-client-tools-install)

- Create your Play project  
	 ``activator new playshifted``
    
- Git your Play project  
	 ``git init``
  
- Create a DIY app with RHC command line tools  
	 ``rhc app create -a playshifted diy-0.1 --no-git``
  
- Go to your Openshift console
	- Upload your pub key to get your SSH git repo URL (update: RHC client tools does this for you)
	- copy git repo url to clipboard (ssh://somehash@playshifted-yourdomain.rhcloud.com/~/git/playshifted.git)

- Back in Windows/Ubuntu; add openshift as remote repo  
	``git remote add openshift {paste git repo url}``

- Pull from openshift repo (you'll get .openshift folder)  
	``git pull -s recursive -X theirs openshift master``

- Copy [Openshift scripts](https://github.com/thomson256/play-diy-scripts) into your Play project folder's root (you can inspect _openshift_deploy_,  _start_, _stop_ and _load config_ if you like to see details)  
Note that ``target`` folder is _not_ excluded. This might be a matter of taste, but doing compile on desktop works better in larger projects.
Resulting file structure
	.git  
	.openshift  
	app  
	conf  
	{and the rest...}  
	gitignore  
	openshift_deploy  

- Remove default Ruby code, folder ``diy``
	

- Create Openshift specific application.conf by creating _openshift.conf_ on _playshifted/conf_ and do necessary configurations which include db settings:  

		db.default.url="jdbc:mysql://"${OPENSHIFT_DB_HOST}":"${OPENSHIFT_DB_PORT}/${OPENSHIFT_APP_NAME}  
		db.default.user=${OPENSHIFT_DB_USERNAME}    
		db.default.password=${OPENSHIFT_DB_PASSWORD}  
	
- Run your Play app to see it's working locally 
	``activator run``  


Play app running on Openshift is now essentially configured. The following procedures need to be performed now and repeat always when you update your application.


- Ready your Play app  
	``activator clean compile ``  
	``activator stage``  

**Note on Windows**  
_You'll get errors about chmod. Not supported under Windows. Explained [here](http://play.lighthouseapp.com/projects/82401/tickets/252-console-command-stage-doesnt-work-on-windows)  
But really you don't have to care about this.._


- Update Openshift GIT  
	``git add .``  
	``git commit -m 'initial'``  
	``git push openshift``



# Java 8 update

- Install Java 8 as pointed out here: 
http://tecadmin.net/install-java-8-on-centos-rhel-and-fedora/

You need to log into you Openshift shell 
ssh 123somehash1234567@playshifted-yourdomain.rhcloud.com

		cd ${OPENSHIFT_DATA_DIR}
		wget --no-cookies --no-check-certificate --header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com%2F; oraclelicense=accept-securebackup-cookie" "http://download.oracle.com/otn-pub/java/jdk/8u40-b25/jdk-8u40-linux-x64.tar.gz"

		tar xzf jdk-8u40-linux-x64.tar.gz

- Test Java 8
	- Set env variable for this session:

		export JAVA_HOME="$OPENSHIFT_DATA_DIR/jdk1.8.0_40"
		export PATH=$JAVA_HOME/bin:$PATH
	
		java -version

- Same JAVA_HOME path on ``.openshift/action_hooks/start`` so it will be always there for Play as well
	
		#java 8
		export JAVA_HOME="$OPENSHIFT_DATA_DIR/jdk1.8.0_40"
		export PATH=$JAVA_HOME/bin:$PATH

