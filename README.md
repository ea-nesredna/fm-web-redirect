# A Redirect script for FileMaker Pro and FileMaker Server

Copyright 2026, Eric Andersen eric@nesredna.com

## Description

Most email clients strongly dislike URLs that are not http or https and will disable the
links so people can't click on them (not complainin, just sayin). This is a good thing. 

But it's a bad thing when you want to send an email that has a link in it to open a 
FileMaker application for a user in FileMaker Pro. If, for example, you are emailing links
to internal users that allows them to open a particular record in a particular layout, sending 
a "fmp://" link to their email will likely result in a link that can't be clicked. 

## redirect.html

This is the main html file that handles the redirect. Put this ion your web server (works
great on FileMaker Server) and make sure it's ready to go. 

1. On FileMaker Server for MacOS, put it in /Library/FileMaker\ Server/HTTPServer/htdocs/httpsRoot
2. For FileMaker Server on Ubuntu Linux running Nginx it goes in /opt/FileMaker/FileMaker Server/NginxServer/htdocs/httpsRoot
3. Change the permissions of the file so it's owned by fmserver:fmsadmin `sudo chown fmserver:fmsadmin redirect.html` (or something like that - depends on your OS)
4. Don't forget the permissions `sudo chmod 755 redirect.html`
5. Restart the FM http server (fmsadmin -u admin restart httpserver)

* Hostname Note *
If your web server and Filemaker server are running on different machines, change the "fmserver = " line to point to the machine your FileMaker app is hosted on. 

## The URL Format

Once you're ready, create a script that emails a link. The format of the link in the email is as follows: 

https://www.example.com/redirect.html?FileMakerAppName?script=ScriptName?$scriptParam1=param1&$scriptParam2=param2

(Yes - there are 2 '?' in the url making it invalid in most of the world, but it will work here.)

- www.example.com = your web server where the redirect.html page is located
- redirect.html = the redirect.html page (but you knew that already)
- FileMakerAppName = the name of the deployed FileMaker app on your FileMaker server. You do not need the .fmp12 extension. You *DO* need to URL encode it so if there are spaces or other characters, make sure you take care of that in your script step. 
- ScriptName = If you want a script to run when the app opens, put the name of the script here. Also, make sure you URL encode the script name. 
- param1 and param2 = if you need to pass variables to the script (UUID, primary key, etc) you can add them here. It will be passed to the script as a variable with no need to do a Get (ScripParameter) script step. Also, make sure you URL encode anything here, and don't pass any secure data in the url. 

## Final notes

1. If the FileMaker app is not open on the user's machine when they click on the link in their email, and the FileMaker app requires a login, the script will not run. The app will open, they will log in, and whatever default login screen they get will appear. They will need to go back to the link in their email and click on it again to get the script to run. 
