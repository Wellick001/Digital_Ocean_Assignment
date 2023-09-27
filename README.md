# Contineous Deployment with Github Actions to a Flask Application on a Droplet
---

I ran into quite some problems while doing this assignment. Most of them really had to do with how a lot of the systems run and communicate with each other. I learned a lot from this.

Before starting this assignment I had already succesfully walked through the previous theory and exercises. So I had my Droplet running, understood the basics of GitHub Actions and had managed to succesfully run a flask application on my Droplet. However combining all of these in a smooth CICD pipeline was quite the challenge. I tried to tackle this step by step, while contineously checking my results.  
 
First I extended my previous .yaml file (that consisted of a run-tests section) with a 'deploy' section that would only deploy if the app passed the tests. I had to figure out how to log in to my Droplet over ssh without a password. For this I used the secrets option in Github while following a small tutorial online about properly registering your keys in the Droplet [medium] (https://medium.com/swlh/how-to-deploy-your-application-to-digital-ocean-using-github-actions-and-save-up-on-ci-cd-costs-74b7315facc2).

I registered the following 3 secrets:

![](/pics/secrets.png?raw=true)

Then I added the following code to my .yaml file:

![](/pics/yaml-code.png?raw=true)

I struggled a bit here. Even though following the tutorial closely, I made some small copy paste or typo's that prevented this from working properly. After carefully backchecking I finally got it working. 

After that I was able to run some scripts on my Droplet. I first checked simple things like mkdir or touch to see if I could write files. After that seemed to be working properly I tried to pull my git with the $ pull git command. Here I got stuck again, with the following error:

```
... err: fatal: not a git repository (or any of the parent directories): .git
```

After a while I understood I couldn't pull my git repo to a folder that wasn't a git repo i.e. had a .git file.

I updated my code by cloning the repo first to the droplet with the following command:

```
$ git clone git@github.com:MaartenBrijker/winc-cd.git
```

After doing this once I deleted the clone command and my git pull worked perfect.

After this seemed to work I had to backtrace some of the steps we earlier had done in the exercises to configure nginx deployment on my custom winc-cd folder inside /home/.

I did this by creating a winc-cd.service file inside /etc/systemd/system/ and modifying the code from farm.service.

After that I edited the default file in /etc/nginx/sites-available/ to load my winc-cd folder as default on the domain.

I ran the $ systemctl enable --now winc-cd command but here I got into a problem again.

Checking $ systemctl status winc-cd I noticed that the program had an error connection to the default port
```
 error: Connection In Use (‘127.0.0.1’, 8000).
```

I thought that $ systemctl restart nginx would resolve this, but somehow it didn't.

However when I ran $ systemctl restart winc-cd, I got it to work.

Now my application was fully working. If I modified my lib.py file to purposely fail the pytest, the program wouldn't deploy. And if I modified my main.py file I would see the changes on my server after deployment.

Lastly I added some commands to my script to make sure that my packages would be up-to-date on my Droplet.

![](/pics/script.png?raw=true)
