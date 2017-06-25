

# Using Python and Crontab to collect streaming data.

In this post, i’ll show an example of how to collect a dataset using a python script and crontab. Crontab is a linux? program that allows you to schedule ‘jobs’ to run at specified intervals.



First I created a python script that reads data from a real-time feed and writes that data to a SQL database. 

Then I use crontab to run the script every half hour.

Typing the following command at the terminal will open up your crontab file in vim (a text editor). If you haven’t set up a cron job before this will be empty.
```
crontab -e
```

To add a job, type ‘i’ to enter insert mode. You need to specify when/how often you want it to run, and the script that you want it to run. You can set it up to run every x minutes/hours/days, or run at a specific time each day.

Figuring the correct syntax for the timing can be a little tricky. Luckily there are many examples and guides you can find online. I want my script to be run every 30 minutes, for which the timing part is written as:

```
*/30 * * * * 
```


When you’re done, hit escape to exit insert mode, then colon the  type ‘wq’ and enter. ‘wq’ writes (w) the changes to file, and then quits (q) vim and returns you to the terminal.

It can be hard to figure out the right syntax for the cronjob and debug errors.

websites for crontab syntax

<https://crontab-generator.org/>

Some problems and solutions I encountered:

- If running a python script, you have to put the absolute python path. To run a python script from the terminal, you would normally type

```
python my_script.py
```

However, I found this didn’t work in a cron job; I got an error that module X doesn’t exist. Cron doesn’t read you’re bash profile (where your python path is probably set), so you have to give the full path to your python. For example:

```
*/30 * * * * /Users/Andy/anaconda3/bin/python/ my_script.py
```

See <https://serverfault.com/questions/616889/python-script-succeeds-manually-but-fails-on-crontab>

I believe another option would be to tell cron to run a shell script instead, and have that script call your python script.


- It’s a good idea to save the raw data, rather than making modifications to it and saving those results (or saving both). You never know what exactly you might end up using the data for in the future, and if you saved the raw data you can always start from that. And if you find a bug in your script later on, you will be able to fix it and apply it to the raw data. Otherwise, you might have wasted months of data collection!

- Write some kind of log file or output to help debug your cronjob and monitor that it is working correctly.

- If your cronjob produces an error or warning, you will get ‘mail’ in the terminal. (I had no idea before this that your computer could send you ‘mail’). Typing mail in the terminal will lest all the messages. This can be useful for degubbing.


Save to S3?