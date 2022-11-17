---
title: "Obsidian in GitHub and hotkeys"
date: 2022-11-16T19:40:41Z
draft: false
tags: ["personal", "aws"]
categories: ["projects"]
featuredImage: "/img/obsidian.jpg"
---

After a week of using Obsidian I felt in love.
I used to be using OneNote to take my notes and organize them into folders.
But, one thing I missed was the ability to quickly format my notes with Markdown.
I got used to Markdown since I started using Rmarkdown (like 5 years ago) and then after moving to Jupyter.
So the versatility to format words, add photos and use Mathjax for technical notes were things that I couldn't fit in with OneNote. 

But something I liked about OneNote was the cloud sync feature.
But this awesome [article Bryan Jenks](https://medium.com/analytics-vidhya/how-i-put-my-mind-under-version-control-24caea37b8a5) opens up the possibility
to keep notes synced with GitHub, an that's sick. 
Because one of the best features of Markdown is the ability to track changes,
since it's plain text, a big difference from Word files. Basically the steps are:

1. Create an empty repo
2. Add these lines to ignore files:
	.obsidian/cache
	.trash/
	.DS_store
3. Do the init commit:
4. Run a script to automate the commits and the push
	Because my computer uses windows, I prefer using powershell script, for this you need to install [posh-git](https://git-scm.com/book/en/v2/Appendix-A%3A-Git-in-Other-Environments-Git-in-PowerShell) and write the following code:
	
```
$vault_path="YOUR ABSOLUTE VAULT PATH"
cd "$vault_path"

$CHANGES_EXIST="$(git status --porcelain | wc -l)"

if($CHANGES_EXIST -eq 0){exit;}

git pull
git add .
git commit -q -m "Last Sync: $(Get-Date -format 'u')"
git push -q
``` 

and save it as obsidian_script.ps1
	
5. Run the script with cron:
	Access to task schuduler with Win+R and taskschd.msc, then
	Click in Create Basic Task
	Set the trigger
	Put the path of the shell script
	And it is done

Bonus step:
 6. If you want to create a shortcut to run this script whenever you want, you can use AutoHotkey. 
    Using the above script we create a file called obsidian.ahk. Once executed, the shortcut is Ctrl + Shift + o.
    You can set this ahk file in the task schuduler to start with Windows or adding a shortcut for the ahk file in the startup folder (Win + R and type shell:startup). The code is:

``` 
^+o::
run powershell.exe -WindowStyle hidden -File "YOUR obsidian.ps1 PATH"
return
```

References:

- [How I Put My Mind Under Version Control | by Bryan Jenks | Analytics Vidhya | Medium](https://medium.com/analytics-vidhya/how-i-put-my-mind-under-version-control-24caea37b8a5)
- [How To Launch Git Bash from Windows Command Line? - Stack Overflow](https://stackoverflow.com/questions/17302977/how-to-launch-git-bash-from-windows-command-line)
- [Scheduled tasks and cron jobs on Windows (active-directory-wp.com)](https://active-directory-wp.com/docs/Usage/How_to_add_a_cron_job_on_Windows/Scheduled_tasks_and_cron_jobs_on_Windows/index.html)
- [Git - Git in PowerShell (git-scm.com)](https://git-scm.com/book/en/v2/Appendix-A%3A-Git-in-Other-Environments-Git-in-PowerShell)