# Docker Swarm

I'm using an old [Samsung R580 laptop](https://www.cnet.com/products/samsung-r580-jsb1-15-6-core-i5-430m-4-gb-ram-500-gb-hdd/specs/) to create a docker swarm cluster of one.  I plan on adding a couple of 1u servers to this later, as I can afford it.  Once I'm done testing pfSense on the Asus EEE Netbook, I'll most likely bring it into the cluster as a backup until that happens.

Docker has grown a lot over the past few years that I have been seriously playing around with it and using it at work.  Docker in swarm mode makes so many things possible quickly and simply.  I'll go over the steps for installation and deployment of our docker swarm cluster of one and give some quick pointers and DOs and DONTs if I can think of any along the way.

## You will need
* 1 to 1000+ physical hosts, vms, ec2 instances, or whatever will run docker.
* A user with key authenticated ssh access and sudo privileges
* I'm working with ubuntu 16.04, but docker works on many other platforms
  * https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/
  * I'm using [ansible](https://github.com/jahrik/home_lab/blob/master/swarm.yml) to bring it or any nodes I specify in an inventory.ini file into the swarm cluster as either masters or workers.
  * I also run [a few ansible scripts](https://github.com/jahrik/home_lab/blob/master/site.yml) at it to ignore the lid closing and setup nfs shares

### Gaining some visualization into the cluster

You can easily manually check up on the health of your cluster from the command line with a few simple commands or perhaps corporate only needs a dashboard to look at and doesn't care for your nerdy commands.
```
docker service ls                                                                                     
ID                  NAME                MODE                REPLICAS            IMAGE                             PORTS
t9h59ml34qmh        viz_viz             replicated          1/1                 dockersamples/visualizer:latest   *:8080->8080/tcp
```

But sometimes it's nice to have a more visual display.  This tool, [docker visualiser](https://github.com/dockersamples/docker-swarm-visualizer) is just what you need for such a task.

![alt text](https://github.com/dockersamples/docker-swarm-visualizer/raw/master/nodes.png)

Here is a compose v3 stack file that should work for you
```
version: '3'

services:

  viz:
    image: dockersamples/visualizer
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    ports:
        - '8080:8080'
    deploy:
      replicas: 1
```

Save this to a file viz-stack.yml
and start it up.
```
docker stack deploy -c viz-stack.yml viz
Creating network viz_default
Creating service viz_viz
```

Check for the running service
```
docker service ls
ID                  NAME                MODE                REPLICAS            IMAGE                             PORTS
t9h59ml34qmh        viz_viz             replicated          1/1                 dockersamples/visualizer:latest   *:8080->8080/tcp
```

And finally, follow the log output

```
docker service logs -f viz_viz
viz_viz.1.nye7i9v0vr2e@master.dojo.io    | npm info it worked if it ends with ok
viz_viz.1.nye7i9v0vr2e@master.dojo.io    | npm info using npm@5.3.0
viz_viz.1.nye7i9v0vr2e@master.dojo.io    | npm info using node@v8.2.1
viz_viz.1.nye7i9v0vr2e@master.dojo.io    | npm info lifecycle swarmVisualizer@0.0.1~prestart: swarmVisualizer@0.0.1
viz_viz.1.nye7i9v0vr2e@master.dojo.io    | npm info lifecycle swarmVisualizer@0.0.1~start: swarmVisualizer@0.0.1
viz_viz.1.nye7i9v0vr2e@master.dojo.io    |
viz_viz.1.nye7i9v0vr2e@master.dojo.io    | > swarmVisualizer@0.0.1 start /app
viz_viz.1.nye7i9v0vr2e@master.dojo.io    | > node server.js
viz_viz.1.nye7i9v0vr2e@master.dojo.io    |
```

With that you should be able to browse to [http://localhost:8080](http://localhost:8080) and check it out.

#### ghost-stack.yml
[ghost](https://hub.docker.com/_/ghost/)
Mounting a volume on the local machine at `/data/ghost/content`, for persistent data retention on a restart of the docker service.
```
version: '3'

services:

  blog:
    image: ghost:1-alpine
    volumes:
      - /data/ghost/content:/var/lib/ghost/content
    ports:
        - '2368:2368'
    deploy:
      replicas: 1
```

#### stress-stack.yml
For stress testing the system.
```
version: '3'

services:

  stress:
    image: progrium/stress
    command: '--cpu 2 --io 1 --vm 2 --vm-bytes 128M --timeout 60s'
    deploy:
      replicas: 1
```
