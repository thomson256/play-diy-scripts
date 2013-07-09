play-diy-scripts
================

Openshift deployment scripts for Play! app to get it running on Openshift

Copy all of the contest of ``copy-these`` *including* gitignore file and paste & replace into your Play app's dir according to instructions.


Deploy Play framework application to Openshift
==============================================

*Tested with Play Framework 2.1.1*

- Install Openshift [command line tools](https://www.openshift.com/developers/rhc-client-tools-install)

- Create your Play project  
	 ``play new playshifted``
    
- Git your Play project  
	 ``git init``
  
- Create a DIY app with RHC command line tools  
	 ``rhc app create -a playshifted diy-0.1 --no-git``
  
- Go to your Openshift console
	- Upload your pub key to get your SSH git repo URL
	- copy git repo url to clipboard (ssh://somehash@playshifted-yourdomain.rhcloud.com/~/git/playshifted.git)

- Back in Windows; add openshift as remote repo  
	``git remote add openshift {paste git repo url}``

- Pull from openshift repo (you'll get .openshift folder)  
	``git pull -s recursive -X theirs openshift master``

- Copy [Openshift scripts](https://github.com/thomson256/play-diy-scripts) into your Play project folder's root (you can inspect _openshift_deploy_,  _start_ and _load config_ if you like to see details)  
Note for example that in gitignore the target folder is excluded.  
Resultin file structure
	.git  
	.openshift  
	app  
	conf  
	{and the rest...}  
	gitignore  
	openshift_deploy  
	

- Create Openshift specific application.conf by creating _openshift.conf_ on _playshifted/conf_ and do necessary configurations which include db settings:  
	db.default.url="jdbc:mysql://"${OPENSHIFT_DB_HOST}":"${OPENSHIFT_DB_PORT}/${OPENSHIFT_APP_NAME}  
	db.default.user=${OPENSHIFT_DB_USERNAME}    
	db.default.password=${OPENSHIFT_DB_PASSWORD}  
	
- Run your Play app to see it's working  
	``play run``  


Play app running on Openshift is now essentially configured. The following procedures need to be performed now and repeat always when you update your application.


- Ready your Play app  
	``play clean compile ``  
	``play stage``  

**Note on Windows**  
_You'll get errors about chmod. Not supported under Windows. Explained [here](http://play.lighthouseapp.com/projects/82401/tickets/252-console-command-stage-doesnt-work-on-windows)  
But really you don't have to care about this.._


- Update Openshift GIT  
	``git add .``  
	``git commit -m 'initial'``  
	``git push openshift``

