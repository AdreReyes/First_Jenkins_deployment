# Documentation

So, first I created an Ubuntu EC2 on AWS
How I set up AWS EC2:
Logged in 
From left sided menu, selected Instances and clicked “Launch instances” 
I named my instance and selected Ubuntu, I created a key pair and saved that to my local computer then uploaded it to Google Drive so I can download it from Google Drive onto my Ubuntu system files
I created a new security group and you have to pay attention to the name that it has so that later you can have an idea of which security group to edit after set up. It will have the same name. I checked the “allow ssh” box and changed the dropdown to my IP and I checked the box for “allow http”.
I left the default storage the same and lastly, clicked on the orange button “Launch instance”.
After setting up the instance, I went back to the left sided menu and selected Security Groups. I clicked on the security group under “create security ID” that has the same name as the one I created when I set up my EC2.
Note: Although I created a security group at the time that I set up my instance, AWS doesn’t allow you to set up the ports and rules right then and there so now we will do that. 
At the bottom of the page I selected edit inbound rules and clicked on “edit inbound rules”.  For rule 1, I set the type to SSH and the port range should be 22. I selected my IP for source type because I want to SSH via my IP and I don’t want anyone else to be able to get into my local host server and only rely on my key for protection. I want that extra layer of protection which is why I set it to only my IP. 
I needed ports 80, 8080, and 22 open so for rule 2, I set the type to HTTP and the port range should be 80. I selected anywhere for source type and the Anywhere-IPv(#) that matches up with the one my EC2 instance was set up with. 
For rule 3, I set the type to Custom TCP and the port range should be 80. I selected anywhere for source type and the Anywhere-IPv(#) that matches up with the one my EC2 instance was set up with. Source should be 0.0.0.0/0
Yay! We’ve set up our Ubuntu EC2. 
Now to install and set up Jenkins: At first, I did it manually (I put in the series of commands to install  python3-pip, python3-10-ven, and Jenkins and run it from my terminal in Ubuntu and SSHed into the local host ) but then I accidentally deleted my volume that I made for the EC2 during 8/31 class and I lost all of my work. Luckily, a classmate helped me out so I didn't have to spend hours installing and setting up Jenkins and inputting the commands again so I will just document this second experience. 
First, I SSHed remotely into my EC2 by running the following command <ssh -i /path/key-pair-name.pem instance-user-name@instance-public-dns-name> and the pem file is in my Ubuntu files downloaded from the AWS EC2 instance creation steps. I wrote out the path to the file and pasted the other information into the command. 
After sshing into the EC2 remotely, from this github repo https://github.com/RichardDeodutt/Jenkins-Setup I copied the code from  installjenkins.sh *with permission* and pasted it in a new .sh file and ran that script.  That installed the python3-pip and  python3-10-venv packages AND installed Jenkins. 
With the second script titled “runinstalljenkins.sh” I repeated my steps from step 2 and ran the script. This script runs Jenkins and gives me the secret admin password needed to set up Jenkins. 
Now, you have to go to your browser, it doesn’t matter where and type in your IP address and port you are running Jenikins from. Ex. https://myIPadress:portnumber You should see Jenkins open up in the browser. After entering the password, you have to create your Jenkins profile by answering the profile questions and set your username/password. 

Yay! The Jenkins account is set up. Connecting Github to Jenkins Server:
Go to your Github and create an access token by selecting settings  >> Developer settings >> Personal access tokens >> Generate a new token >> set the permissions for it (check mark repo and admin:repo_hook) >> Generate token >> put this token somewhere
Go back to Jenkins and select “new item”
Where it says enter an item name type in url-shortener and select multibranch pipeline 
A form will pop up. Enter a display name, in this case it would be “Build Flask” and a brief description which would be “CI/CD pipeline deployment 1”. 
Scroll down where it says “branch sources” and select add source then select Github. This should open up the branch sources tab in Jenkins where under Github credentials you would select add and select Jenkins. Another popup form will appear where it will ask you to enter in your credentials. Enter in your username where it says username and Github token where it says password. Select add all the way at the bottom.
Now in your Github credentials dropdown you should see the credentials you just added option. Select that, then head back over to Github and fork the repo then paste that in the text box where it says Repository HTTPS URL (for this assignment it should be: https://github.com/kura-labs-org/kuralabs_deployment_1. git) and validate it. If validation is okay, select save button. 
Now under Branch sources it should say Build Configuration and in its Mode text box it should say “by Jenkinsfile” and in the script path text box it should says “Jenkinsfile”. Select Apply and then Save.
Now a build should be happening where it says “Build History”  in the left sided menu 

Yay! We created a build in Jenkins. Deploy to Elastic Beanstalk:
Open up your Ubuntu terminal or local terminal on your device and type in “git clone <deployment repo files>” in this case the deployment repo files were in https://github.com/kura-labs-org/kuralabs_deployment_1.git this link. I did mine on my Mac terminal. Ls so that you can see if a directory was created. 
Next zip the files. To zip the files I  ran this command: zip ../kuralabs_deployment_1.zip -r * .[^.]*
Now that I have the zip file. I go to Elastic Beanstalk. Select “create a new environment”, then select “web server environment”, and enter the following configurations: 						
Application name: url-shortner
Environment name: Urlshortner-env
Platform: Python
Platform branch: 3.8
Platform version: 3.3.17
Application code: Upload my zip file
Then select “create environment”. Give Elastic Beanstalk a few minutes to finish creating the environment. Now to know if the deployment worked, there should be a link in blue that brings you to the actual application page. 
Improvements: I would say that I can improve on utilizing scripts to make my life easier and not just utilize them but make my very own. 
