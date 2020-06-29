---
layout: post
title: Using Cron to Automate Running Python and R Scripts
tags: Python,R
---

When you're working on data science projects, you might have scripts/processes you want to run each day. For example, i'm working on a [project](https://github.com/andypicke/JeffCo-OpenSpace-LotSpot-Analysis) to model how full trailhead parking lots are based in part on weather. I want to collect the weather forecast each day so I can use it to make predictions. Instead of manually running the script every day (which I would probably forget to do anyways), I set up a cron job that automatically runs the script each day. Cron can also be useful to automate other processes like backups etc..

[Cron](https://en.wikipedia.org/wiki/Cron) is a job scheduler that comes with Linux or Mac OS (Windows has a similar utility called [Task Scheduler](https://www.schakko.de/2020/03/15/how-to-add-a-cron-job-scheduled-task-on-windows/#:~:text=On%20Microsoft%20Windows'%2C%20cron%20jobs,with%20help%20of%20schtasks.exe.&text=On%20modern%20Windows%20systems%20(Windows,is%20available%20in%20version%202.0.))) . In this post, i’ll show a simple example of how I used cron to automate some Python and R scripts.

To add or edit a cron job, type ```crontab -e``` at the terminal, which will open up the crontab file in your default text editor ([Vim](https://en.wikipedia.org/wiki/Vim_(text_editor)) in my case). If you haven’t set up a cron job before this will be empty.

To add or edit a job, type ```i``` to enter insert mode in Vim. You need to specify when/how often you want it to run, and the script that you want it to run. You can set it up to run every x minutes/hours/days, or run at a specific time each day.

Figuring out the correct syntax for the timing can be a little tricky sometimes, but there are many great examples and [guides](https://crontab-generator.org/) you can find online. Below is my crontab entry for a script that downloads the weather forecast from an API at 12:10 AM each day:

```
10 0 * * * source ~/.bash_profile && cd ~/Galvanize/Lot-Spot_Analysis && /opt/anaconda3/bin/python ./src/get_DarkSky_forecast_job.py
```

When you’re done setting up the job, hit escape to exit insert mode, then colon, then type ‘wq’ and enter. ‘wq’ writes (w) the changes to file, and then quits (q) Vim and returns you to the terminal.

Some things to note:
* The first part ```10 0 * * *```  tells cron to run the following commands at 12:10 AM each day.
* Cron is not aware of your bash_profile, so you need to explicity source it if you need it. For example my script uses the Python [os](https://docs.python.org/3/library/os.html) library to read an API key I defined in my bash_profile.
* You can combine multiple commands with ```&&```.
* You can specify the full filepath to your script, or cd into the directory first and run relative to there.
* If running a python script, you have to put the absolute python path. To run a python script from the terminal, you would normally just type ```python my_script.py```, but this doesn't work in cron. To find your full python path, type ```which python``` at the terminal.
* If your cronjob produces a print output or error, you will get ‘mail’ in the terminal (I had no idea before that this existed). Typing ```mail``` in the terminal will list all the messages. This can be useful for degubbing. For information on how to suppress output or even email output to a different address, check out this [post](https://tecadmin.net/disable-crontab-output-email-notifications/#:~:text=You%20can%20easily%20suppress%20output,you%20want%20to%20suppress%20output.&text=This%20is%20more%20useful%20for%20the%20cron%20jobs%20running%20wget%20command.).

If you want to set up a cron job to run a R script, you'll need to run it with [Rscript](https://linux.die.net/man/1/rscript), which will look something like this:
```
10 0 * * * /usr/local/bin/Rscript R_script_to_run.R
```
You can type ```which Rscript``` in terminal to get the full path.

I hope you found this information useful!

