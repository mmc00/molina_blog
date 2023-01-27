---
title: "Obsidian in GitHub and hotkeys"
date: 2022-11-16T19:40:41Z
lastmod: 2023-01-26
draft: false
tags: ["personal", "aws"]
categories: ["projects"]
featuredImage: "/img/obsidian.jpg"
---

After using Obsidian for a week, I fell in love with it. 
Previously, I had been using OneNote to take and organize my notes in folders.
However, I missed the ability to quickly format my notes using Markdown, 
which I had grown accustomed to using with Rmarkdown and Jupyter. 
The versatility to format text, add images,
and use Mathjax for technical notes was something that OneNote couldn't provide.

One thing I did like about OneNote was the cloud synchronization feature.
However,
[this awesome article by Bryan Jenks](https://medium.com/analytics-vidhya/how-i-put-my-mind-under-version-control-24caea37b8a5) opened up the possibility 
of keeping my notes synced with GitHub, which is amazing. 
One of the best features of Markdown is the ability to track changes,
since it's plain text and much different from Word files. 
Here are the basic steps to set this up:

1.  Create an empty repository
2.  Add the following lines to the ignore file: .obsidian/cache .trash/ .DS_store
3.  Do the initial commit
4.  Run a script to automate the commits and push.
Since my computer runs on Windows, I prefer using a PowerShell script.
To do this, you need to install 
[posh-git](https://git-scm.com/book/en/v2/Appendix-A%3A-Git-in-Other-Environments-Git-in-PowerShell) 
and write the following code for Windows:

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

For Windows, save the script as obsidian_script.ps1. For Mac, use the following code:

```
#!/usr/bin/env sh
vault_path="YOUR ABSOLUTE VAULT PATH"
cd "$vault_path"

git pull 

CHANGES_EXIST="$(git status --porcelain | wc -l)"

if [ "$CHANGES_EXIST" -eq 0 ]; then
	exit 0
fi

git pull
git add .
git commit -q -m "Last Sync: $(date +"%Y-%m-%d %H:%M:%S")"
git push -q
```

5. To run the script using cron on Windows:

	Access the task scheduler by pressing Win+R and typing "taskschd.msc"
	Click "Create Basic Task"
	Set the trigger for when you want the script to run
	Enter the path of the shell script
	Click "Finish" and the task is set up.
	
On Mac, you can use the `crontab` command to schedule the script to run at specific intervals.
For example, to run the script every hour, you can use the following command:

```
0 * * * * /path/to/obsidian_script.sh
```

<br>

### Bonus step
To create a shortcut to run the script using AutoHotkey on Windows:

1.  Create a file called obsidian.ahk using the provided script.
2.  Run the obsidian.ahk file to create the shortcut. The shortcut will be Ctrl+Shift+o.
3.  To set the ahk file to start with Windows, you can either add it to the task scheduler or add a shortcut for the ahk file in the startup folder. To access the startup folder, press Win+R and type "shell:startup".
4.  The code for the obsidian.ahk file is provided below:

```
^+o::
run powershell.exe -WindowStyle hidden -File "YOUR obsidian.ps1 PATH"
return
```

To create a shortcut to run the script on Mac using Automator:

1.  Open Automator and select "File" then "New" and choose "Quick Action".
2.  Search for "Run Shell Script" and in the "Shell" dropdown, select "/bin/sh".
3.  Add the following command:

```
`sh /usr/local/bin/zk_sync`
```

4.  Name the action and save it.
5.  Go to "System Preferences" then "Keyboard" and select "Shortcuts".
6.  Under "Services", you should find your script. Assign a key combination to it.

<br />
<br />

### References:

- [How I Put My Mind Under Version Control | by Bryan Jenks | Analytics Vidhya | Medium](https://medium.com/analytics-vidhya/how-i-put-my-mind-under-version-control-24caea37b8a5)
- [How To Launch Git Bash from Windows Command Line? - Stack Overflow](https://stackoverflow.com/questions/17302977/how-to-launch-git-bash-from-windows-command-line)
- [Scheduled tasks and cron jobs on Windows (active-directory-wp.com)](https://active-directory-wp.com/docs/Usage/How_to_add_a_cron_job_on_Windows/Scheduled_tasks_and_cron_jobs_on_Windows/index.html)
- [Git - Git in PowerShell (git-scm.com)](https://git-scm.com/book/en/v2/Appendix-A%3A-Git-in-Other-Environments-Git-in-PowerShell)