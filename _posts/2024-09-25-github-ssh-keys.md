---
layout: post
title:  "Adding a Private Repo to ARGOCD with SSH Keys"
categories: [DevOps, ARGOCD]
---
Categories : {{page.categories | capitalize | join: ', '}}

Hey Guys,  
  
Just wanted to make a quick post here. My team has recently been deploying ARGOCD for usage in a test environment and one of the things we needed to do was connect ARGOCD to our Private internal Repos.  
  
Turns out there are a few different ways to do this, and they include SSH Keys, Personal Access Tokens, and even a Github App.

Now if you are connecting alot of different repos, I would highly recommend deploying a Github App so that it handles all of the authentication for all of your repos. however since we are just doing this in a test cluster we decide that SSH keys will work just fine.

### Generate New keys

So step 1 in the process is to generate new keys for your repo, I would highly recommend using a different key for each repo if possible. Below is the command we used to generate a new key for Github using the recommended algorithm of ED25519 256  

{% highlight bash %}
ssh-keygen -t ed25519 -C "Email.Address@example.com"
{% endhighlight %}


This command should prompt you for a storage location and a passphrase, **Currently (September 2024) ARGOCD does NOT support Passphrases on SSH Keys**.

### Add Keys to GitHub

Once you have generated the key you should have to new files, a private key and a public key. You will need to add your public key to the repo so that Argo can authenticate.  
  
Step 1. Login to GitHub and Access the Repo

Step 2. Navigate to Settings and then to Deploy Keys

Step 3. Add new Deploy key. Give the new Key a name and insert the contents of the pub file into the key box. if you need write access select the option now.

![image.png](/images/github-new-keys.png)

### Add repo to ArgoCD

Now that you have added an SSH Deploy key to your repo, you can map the private repo into argo. For this you will need the SSH Path of the repo ( looks something like git@github.com:USER/REPO-NAME.git) , the value of the private key and a name for your Repo in ARGOCD  
  
Step 1. Login into ARGOCD and Click on Settings on the left side

![image.png](/images/argo-settings.png)

Step 2. Click on Repositories

![image.png](/images/repo-button.png)

Step 3. Click on Connect Repo, and Fill out the details you Gathered.

![image.png](/images/repo-details.png)

Step 4. Once Done click on Connect and check the status on the Repo page. If it worked it should be a status of Successful.

![image.png](/images/connection-status.png)