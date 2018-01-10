# The Tale of Edward Botsworth

I'm an avid lurker on reddit and one thing that fascinates me about the site is all the bots.  In some ways they seem to keep reddit running.  Such as the countless Moderator bots for automating tasks.  There must be at least a small percentage of bots for each of the endless subreddits out there.  There's no way humans could keep up with that.  There are also tons of user made bots you'll see pop up for a day or two until they get banned from your favorite subreddits. There are also a few that have their own subreddit; [r/colorizebot](https://www.reddit.com/r/colorizebot/) has to be one of my favorites.  It will take a black and white picture you feed it and colorize it for you.  I find this sort of thing fascinating and wanted to make my own reddit bot.  I already know a bit of python and that is the recommended starting point for most reddit bot projects.

I started with [PRAW](https://praw.readthedocs.io/en/latest/), the Python Reddit API Wrapper and went from there.  I was able to scrape reddit for daily top posts.  Get user info from the posts and whatever else came with it.

Then I tacked on [ChatterBot](https://github.com/gunthercox/ChatterBot) and this quickly evolved into a simple artificially intelligent bot with a storage database in the back end for trying to make sense out of reddit comment strings.  Then made that work on multiple platforms including gitter, twitter, hipchat, and reddit.  At the time of this writing, the plan is to have it check into Reddit on a daily basis and scrape strings of comments from r/all in way that resembles a conversation. Right now this training process has to be run manually.  I ran it quite a few times for about a month and then stopped.  Everything since that time Edward has learned from twitter DM.  Check out [this bit of code](https://github.com/jahrik/edward/blob/cb36f29f736bad50af0f69a1f2d62a3268031c77/edward.py#L305) to get an idea of what the reddit, twitter, and other training modules are doing.

* training list starts as an empty list []
* reach into [www.reddit.com/r/all/](https://www.reddit.com/r/all/)
  * for every submission collect comment chains
    * for every comment in comment chains collect all replies
      * if the comment is not '[deleted]'
      * if reply is not '[removed]'
      * if reply is less than 80 characters
      * append training list
  * Train the bot

After that, I was able to made it work on Twitter using the [tweepy module](https://github.com/tweepy/tweepy).
Check out how I was able to auto-follow followers using multiprocess and sleep [here](https://github.com/jahrik/edward/blob/cb36f29f736bad50af0f69a1f2d62a3268031c77/edward.py#L864).

End result being a bot that I can talk to and train from multiple platforms.  Seems like a great idea on paper.  He's been live for quite a while now and is still dumb as shit, but it's not his fault.  I just haven't had the time to think of better and faster ways of training him.  For a moment I thought I would have it talk to other robots online to advance his speech, but the few that I looked into end up charging you if you want to use it more than just a few times or would just lock you out.

## Make locally
Download the [source code](https://github.com/jahrik/edward) and run the following to make this in your local swarm
```
git clone https://github.com/jahrik/edward.git

cd edward
```

#### Be sure to populate any needed environment variables.
```
# reddit
export REDDIT_CLIENT_ID=''
export REDDIT_CLIENT_SECRET=''
export REDDIT_USERNAME=''
export REDDIT_PASSWORD=''

# twitter
export TWITTER_KEY=''
export TWITTER_SECRET=''
export TWITTER_TOKEN=''
export TWITTER_TOKEN_SECRET=''

# hipchat
export HIPCHAT_HOST=https://api.hipchat.com/
export HIPCHAT_ROOM=
export HIPCHAT_ACCESS_TOKEN=''

# gitter
export GITTER_ROOM=''
export GITTER_API_TOKEN=''

# facebook
export FACEBOOK_API_TOKEN=''
export FACEBOOK_PAGE_TOKEN=''
```

#### Run make to build your docker container
```
make
Sending build context to Docker daemon  263.8MB
Step 1/11 : FROM python:3.7.0a1-stretch
3.7.0a1-stretch: Pulling from library/python
3e17c6eae66c: Pull complete
74d44b20f851: Pull complete
a156217f3fa4: Pull complete
4a1ed13b6faa: Pull complete
c6defea3d225: Extracting [==================================================>]  212.8MB/212.8MB
...
...
Collecting wrapt==1.10.11 (from -r requirements.txt (line 32))
  Downloading wrapt-1.10.11.tar.gz
Collecting tweepy==3.5.0 (from -r requirements.txt (line 33))
  Downloading tweepy-3.5.0-py2.py3-none-any.whl
...
...
Step 11/11 : CMD ["python3","edward.py","-b","twitter"]
 ---> Running in 86f85cde3ffb
Removing intermediate container 86f85cde3ffb
 ---> 745359ea9dd7
Successfully built 745359ea9dd7
Successfully tagged jahrik/bot:0.1.1
Successfully tagged jahrik/bot:latest

```

#### Deploy to docker swarm
It is hosted on Docker swarm using a compose v3 yml file and runs as multiple services that will automatically restart on failure and allows for individual containers for each given service running; twitter, hipchat, gitter, mongo.  Check it out here [docker-stack.yml](https://github.com/jahrik/edward/blob/master/docker-stack.yml)

```
version: '3'

services:

  twitter_bot:
    image: jahrik/bot:latest
    environment:
      - TWITTER_KEY=${TWITTER_KEY}
      - TWITTER_SECRET=${TWITTER_SECRET}
      - TWITTER_TOKEN=${TWITTER_TOKEN}
      - TWITTER_TOKEN_SECRET=${TWITTER_TOKEN_SECRET}
    depends_on:
      - mongo
    command: python3 edward.py -b twitter

  hipchat_bot:
    image: jahrik/bot:latest
    environment:
      - HIPCHAT_HOST=${HIPCHAT_HOST}
      - HIPCHAT_ROOM=${HIPCHAT_ROOM}
      - HIPCHAT_ACCESS_TOKEN=${HIPCHAT_ACCESS_TOKEN}
    depends_on:
      - mongo
    command: python3 edward.py -b hipchat

  gitter_bot:
    image: jahrik/bot:latest
    environment:
      - GITTER_ROOM=${GITTER_ROOM}
      - GITTER_API_TOKEN=${GITTER_API_TOKEN}
    depends_on:
      - mongo
    command: python3 edward.py -b gitter

  mongo:
    image: mongo:latest
    environment:
      - MONGO_DATA_DIR=/data/db
      # - MONGO_LOG_DIR=/dev/null
    volumes:
      - ./data/db:/data/db
    ports:
      - 27017:27017
    command: mongod
```

You can run this deployment with `docker stack deploy -c stack.yml bot`  or use the Makefile provided
Running `make deploy` will also run docker build first again.
```
make deploy
Sending build context to Docker daemon  263.8MB
Step 1/11 : FROM python:3.7.0a1-stretch
 ---> 3f04e51f06a1
...
...
Step 10/11 : RUN pip install -r requirements.txt
 ---> Using cache
 ---> 1e4c2f05fad3
Step 11/11 : CMD ["python3","edward.py","-b","twitter"]
 ---> Using cache
 ---> 745359ea9dd7
Successfully built 745359ea9dd7
Successfully tagged jahrik/bot:0.1.1
Successfully tagged jahrik/bot:latest
Creating network edward_default
Creating service edward_mongo
Creating service edward_twitter_bot
Creating service edward_hipchat_bot
Creating service edward_gitter_bot
```

Check these running services
```
docker service ls
ID                  NAME                 MODE                REPLICAS            IMAGE               PORTS
uhupkuwb8i9m        edward_gitter_bot    replicated          1/1                 jahrik/bot:latest
ksq2svnphp70        edward_hipchat_bot   replicated          1/1                 jahrik/bot:latest
b93o23brati8        edward_mongo         replicated          0/1                 mongo:latest        *:27017->27017/tcp
re1zn2n3n9ff        edward_twitter_bot   replicated          1/1                 jahrik/bot:latest
```

Check and see what the hell mongo is doing
```
docker service logs -f edward_mongo
```

Alternatively you can run this with docker compose with
```
make test
```

And to bring the whole thing down for both run
```
make destroy
```

### PRAW and Chatterbot meet tweepy
* Source code on [github](https://github.com/jahrik/edward)
* Follow edward on [twitter](https://twitter.com/edwardbotsworth)
* He should follow you back
* Private message him to see what he has scraped from reddit r/all

[![Join the chat at https://gitter.im/jahrik/edward](https://badges.gitter.im/jahrik/edward.svg)](https://gitter.im/jahrik/edward?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)
