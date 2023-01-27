---
title: "Git: using multiple accounts"
date: 2023-01-26T21:20:50Z
draft: false
tags: ["personal", "git"]
categories: ["mix"]
---

Recently, I received a Github account for my current job. 
Prior to that, I used to keep my work and personal projects in a single,
personal Github account.
This change required me to adjust my workflow and find a way to manage two 
Github accounts on my computer. To do this, I followed two main tutorials.
[The first](https://gist.github.com/rahularity/86da20fe3858e6b311de068201d279e3) tutorial 
showed me how to create SSH keys and register them with Github. The steps were as follows:

```
# Step 0:
## if you don't have an .ssh already
mkdir ~/.ssh
cd ~/.ssh 

# Step 1:
# remove the quotes
ssh-keygen -t rsa -C "your-email-address" -f "github-username"
  
# Step 2:
ssh-add -K ~/.ssh/personal_marlon
# or 
ssh-add --apple-use-keychain ~/.ssh/personal_marlon

# Step 3:
vim ~/.ssh/personal_marlon.pub
pbcopy < ~/.ssh/personal_marlon.pub

# Step 4:
## Copy those keys into Github: ac

# Please repeat steps 1-4 for your second account.

# Step 5:
## go to where you want to clone the repo
## mkdir ~/personales
git clone git@github.com:personal_marlon/notes.git

# Step 6:
# repeat for second account
git config --global user.email personal_marlon@gmail.com
git config --global user.name personal_marlon
```

I encountered two main problems when following the tutorial. Firstly,
I didn't have an SSH file because I had recently received a new computer for my job. 
If this is your situation, you can simply create one by following 
[this guide](https://superuser.com/questions/635269/cant-find-ssh-directory-in-my-terminal).
Secondly, I didn't fully understand how the process worked, so I referred to a second tutorial.
Thanks to [Jeff Brown](https://jeffbrown.tech/multiple-github-accounts-ssh/),
I was able to understand that if your first account is your work account,
you can clone using the same repository URL. 
However, if the repository you want to clone is from your personal account, 
you need to match the host name assigned in your config file. For example:

```
# original url
git@github.com:personal_marlon/notes.git
# modified name
git@github.com-personal_marlon:personal_marlon/notes.git
```

In my case, my username for my second account is "personal_marlon".
So, if I want to clone a repository from my personal account, 
I would need to match the hostname "personal_marlon" in my config file.

References

- [How To Work With Multiple Github Accounts on your PC](https://gist.github.com/rahularity/86da20fe3858e6b311de068201d279e3)
- [github - Git cli: get user info from username - Stack Overflow](https://stackoverflow.com/questions/7552054/git-cli-get-user-info-from-username)
- [Using Multiple GitHub Accounts and SSH Keys | Jeff Brown Tech](https://jeffbrown.tech/multiple-github-accounts-ssh/)
- [macos - Can't find .ssh directory in my terminal? - Super User](https://superuser.com/questions/635269/cant-find-ssh-directory-in-my-terminal)
- [github - Permission denied using multiple Git user SSH keys - Stack Overflow](https://stackoverflow.com/questions/64465924/permission-denied-using-multiple-git-user-ssh-keys)