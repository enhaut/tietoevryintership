# Virtual Datacenter test environment
## Deployment
If you have [`docker`](https://www.docker.com/) installed, deployment is pretty easy.
Just start containers using:
```bash
$ docker-compose up 
```
And wait for containers to start up.  
Munin web interface is then available at `http://localhost/`

## Dockerfiles
Application contains 2 different containers, so there are 2 `Dockerfiles`.
Both containers use `Ubuntu 22.04`.
### Master's container dockerfile
The master's dockerfile is more complicated than node's one. Master container requires to run
`munin` for gathering metrics from nodes and generating plots; `cron` to be able to run `munin-cron`
repeatedly. `munin-cron` cronjob is automatically added during installation to `/etc/cron.d/munin`.
This script is also executed on container startup to gather metrics immediately after the start,
so user don't have to wait for graphs generation up to 5 minutes until cronjob generates it. Also `apache` webserver is
started during container startup, it provides access to the munin web interface.
  
`munin.conf` file contains munin configuration, it's copied to the container
during building process. The whole munin's "working" directory `/var/run/munin` 
needs to be owned by `munin` user.  
There is also configuration file for apache - `000-default.conf`.
### Node's container dockerfile
Node's dockerfile is much simpler than master's, it also contains installation of dependencies, `munin-node.conf`
configuration file and plugin copying to container.  
Plugin needs to be deployed, `Dockerfile` contains 2 steps to deploy my plugin:
- creating symlink in `/etc/munin/plugins/` to plugin's file,
- defining user plugin needs in `/etc/munin/plugin-conf.d/munin-node`

Container execute a couple of commands after the startup - cleaning the old `munin-node.pid` file,
starting `munin-node` daemon and endless `tail` to keep container running. 

## Multi container  environment
Containers environment is defined in `docker-compose.yml` file. It describes how containers should be built and connected.
There is no `networks` block, containers does not use some special network, there are just mapped ports of containers to
host system ports:
- `4949:4949` - port `4949` of container `node` is exposed to port `4949`
- `80:80` - port `80` of `master` container is exposed to port `80` of host machine

Environment without explicitly defined network could be little problematic - we don't know exact addresses of containers
so munin node needs to be configured to allow all the requests.  
There is also defined waiting for node startup by `master` container. Master container is able immediately after the 
startup gather metrics.

## TCP connections plugin
Solution also contains pretty simple munin plugin - TCP connections watcher. On every run it counts
currently established TCP connections. It's not multiplatform as implementation in Python may suggest, script reads
`/proc/net/tcp` to get all TCP sockets, and it filters the established ones.  
The plugin could be extended of counting also UDP connections, connections in other states, ...

## Motivation
As a student of university, after my exam period, I found out I have plenty of free time. I would like to spend it 
wisely, want to do something meaningful during my summer break. 
