# Docker Swarm

## stack files
* conposer v3
* should I deploy these with ansible?

#### ghost-stack.yml
[ghost](https://hub.docker.com/_/ghost/)
Mounting a volume on the lacal machine at `/data/ghost/content`, for persistant data retention on a restart of the docker service.
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

#### viz-stack.yml
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
