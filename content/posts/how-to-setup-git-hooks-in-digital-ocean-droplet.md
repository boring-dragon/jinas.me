---
title: "How to Setup Git Hooks in Digital Ocean Droplet"
date: 2019-12-05T23:15:55+05:00
draft: true
---

Since the year is 2019 and not 2006 you really should be using git to deploy your application. So you could always use SFTP to get your web app onto your server, but that is not only less secure, but it also WAY SLOWER. While it might work fine the first time, it becomes a pain in the ass for future updates. Using git, pushing to our server is effortless and has virtually no downtime.



Step 1: Install Git

We will install Git onto our server now in a folder called /var/repo/ which is near our Nginx folder of /var/www/site/ . Let’s make the folder now.
```bash
cd /var
mkdir repo && cd repo
```
This will move us into our /var/ directory and then make a new directory called repo/ and then move us into that folder. You should now be inside /var/repo/ when you execute the next commands.

```bash
mkdir site.git && cd site.git
git init --bare
```
The –bare flag might be a new one for you, and that is because it is generally only used on servers. A bare repo is a special kind of repo whose sole purpose is to receive pushes from developers. You can learn more about these types of repositories from the official git site.

We now have a git repository in /var/repo/site.git congratulations!



Step 2: Setting Up Git Hooks

Git repositories have a cool feature called hooks that we are going to use to move our files after a git push. Think of git hooks like webhooks or maybe WordPress hooks. Basically, you can create scripts that execute when certain hooks are triggered. There are three hooks available through Git: pre-receive, post-receive, and update.

We will focus on the post-receive hook which triggers after the repo has fully downloaded your files and completed receiving a push.

To set up hooks we need to move into the hooks directory inside of our site.git folder. In /var/repo/site.git# we can type ls to see all the files and folders inside. You will see the hooks/ directory which we need to cd into.

Once you are inside the hooks/ directory we are going to create the post-receive script. We will be using a new command called touch which makes an empty file.

```bash
sudo nano post-receive
```
Now you will open up a blank file in Nano (terminal text editor). Type the next two lines into the file and save and exit the file.

```bash
#!/bin/sh
git --work-tree=/var/www/site --git-dir=/var/repo/site.git checkout -f
```
Now save and exit (the same way we keep doing it Ctrl + X then Y to confirm the save and enter to save it as /var/repo/site.git/hooks/post-receive. This file is where all of the magic happens.

The –work-tree= tells git where to copy received files to after it has completed. We set it to point to the folder of the application. The –git-dir= tells git where the bare git directory is that has received the files. It is that simple. Make sure that the whole command is on one line (including the checkout -f).

After you save the /var/repo/site.git/hooks/post-receive file, you need to make one last command before we leave this folder and push our files up. The post-receive file needs execution permissions in order to copy these files over. So we can do that really quick with one line of code. Make sure you are still in /var/repo/site.git/hooks/ folder when you type this command:

```bash
sudo chmod +x post-receive
```
That is it! Now when we push to this repository on our server, the files will be placed in our /var/www/site/ directory and Nginx can begin to serve them to our users.

We are now done on our server, for now, we need to exit the ssh session to access our local machine for the next step. Type the following command to end your ssh session.
```
exit
```
Your command prompt should now change to the name of your computer instead of root@localhost#. This indicates you are no longer on your server and you are making changes now to your local computer.



Step 3: Set up our Local Computer to Push to Production

Now that our server is set up to receive the files, let’s set up our local computer so that it can push the files to our server. We will be using git, so make sure that your local computer’s Site directory is under git version control before you continue.

Just like when we push our files up to GitHub, we set up a git remote called origin that represents GitHub. So when we want to push to GitHub we make a command like this git push origin branch-name. This tells git to push the branch (branch name in this example) to our origin remote.

Now in the same fashion that we set up GitHub as a push location, we will also set up our new server as a push location. I like to call this remote by the name production which represents our live production website. The goal is that after we set it up, we can tell it to push to our server from our local computer by just typing the command git push production master and this will push the master branch to our production server. You can continue to push to GitHub using git push origin master but then when you are ready to make changes go live you will run the push to production.

Note: You can create as many “remotes” as you want in git. It is common to have a secondary server called “staging” which is where push your project to for quality testing before pushing to a live site. The staging site is like a beta for you to test internally in a production-like environment before the whole world sees it in real production. In this case, you could create another server (just following the directions in this tutorial again) and create another “remote” called “staging”. Now you can use a command like a git push staging master to push to staging for testing. Then when you feel that the version is safe for the public, you can run git push production master to push it to the production server. You can also set up other remote servers for git backups (like gitlab) or testing servers, or alpha tests and stuff.

To set up a new remote you use the git remote add command. Before you do this make sure you are in the correct location. First of all, you should no longer be on your server. If you still see root@localhost# in your prompt then you are on your server still. Type exit and click enter to leave the server. You should see the name of your computer in the command prompt now. Use cd commands to get into your site project folder that you want to push to the server. In my case, my project is under /Sites/myweb so I type this to get into my folder via the terminal:

```bash
cd Sites/blog
```
Also, make sure you have git setup for this project. I am assuming you have already made at least one commit (but probably many) and you are currently on your master branch which contains your latest production-ready code.

Make sure that you substitute the red text with your domain name or IP address that you use to ssh into your server.

```bash
git remote add production ssh://root@example.com/var/repo/site.git
```
Make sure to substitute the red text with your domain or IP address. The command should mimic the ssh command you use to log into your server. So if you type ssh root@100.100.100.1 to get into your server than 100.100.100.1 is what it should show on the command above.

With this command, we now created a remote called production that sends our files to the new bare git repo we made on our server.

Now once again, assuming your project code is production-ready and is on your master branch then you can type this command to push it up to your server:
```bash
git push production master
```
The output will look just like when you push to GitHub, but this time you are pushing to your own server! Let’s log back into our server now to make sure it worked.



Step 4: Verify our Git Hook Works

Now let’s make sure our code is in the right place. It should be in our /var/www/site/ folder on our server. It is easy to check, just log back into your server and look at the directory.
```bash
ssh root@example.com
```
Remember that example.com needs to be changed to your domain or IP address. Now cd into your Site folder.
```bash
cd /var/www/site
ls
```
You should now see your website pushed to this folder. You can poke around but it should mimic your files from your local computer. Honestly, anything in here is a good sign because it was empty the last time we logged off the server. If you have your Site in here then you have done everything right.

Note: This was extracted from a tutorial I found online created by devmarketer to just specify things

Original tutorial below:

[Tutorial](https://devmarketer.io/learn/deploy-laravel-5-app-lemp-stack-ubuntu-nginx/)