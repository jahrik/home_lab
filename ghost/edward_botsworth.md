# The Tale of Edward Botsworth

I'm an avid redditor and large part of what makes reddit keep working are bots.  I find this sort of thing fascinating and wanted to make my own reddit bot.  I already know a bit of python and that is the recommended starting point for most reddit bot projects.

I started with [PRAW](https://praw.readthedocs.io/en/latest/), the Python Reddit API Wrapper and went from there.  I was able to scrape reddit for daily top posts.  Get user info from the posts and whatever else came with it.  But I needed more!

I started over with [ChatterBot](https://github.com/gunthercox/ChatterBot) and this quickly evolved into a simple artificially intelligent bot that I was talking to on multiple platforms including gitter, twitter DM, hipchat, and reddit.  At the time of this writing it will check into Reddit on a daily basis and scrape strings of comments from r/all in way that somewhat represents a conversation. Check out [this bit of code](https://github.com/jahrik/edward/blob/cb36f29f736bad50af0f69a1f2d62a3268031c77/edward.py#L305) to get an idea of what it's doing. Essentially, if a comment string is at least 5 comments long and within a range of 80 characters or less, it will be saved as a conversation in a Mongo Database.  The amount of times a sentence or word is followed by another word is saved along side every conversation in the database as well and the more you use the bot the smarter it will get.  The creator of Chatterbot goes into much more detail [here](https://chatterbot.readthedocs.io/en/stable/training.html).

After that, I went ahead and made him work on Twitter using the tweepy module.  He is hosted on Docker swarm using a compose v3 yml file and runs as a service that will automatically restart on failures and with multiple replicas if I chose to do so.

## PRAW and Chatterbot meet tweepy
* Source code on [github](https://github.com/jahrik/edward)
* Follow edward on [twitter](https://twitter.com/edwardbotsworth)
* He should follow you back
* Private message him to see what he has scraped from reddit r/all
