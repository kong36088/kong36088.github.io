title: docker使用安装教程（一）
categories: Docker##分类
tags: [Docker,Linux]##标签，多标签格式为 [tag1,tag2,...]
keywords: Docker,Linux##文章关键词，多关键词格式为 keyword1,keywords2,...
description: 个人安装docker小笔记
date: 2016/05/10 14:24:25 
---
本人使用的安装环境是ubuntu15.04
那么，开始~

# 安装docker
首先，安装docker
``` bash
apt-get update
apt-get install docker.io
``` 

安装完成后，输入`docker`查看docker信息

<!--more-->

``` bash
Usage: docker [OPTIONS] COMMAND [arg...]

A self-sufficient runtime for linux containers.

Options:
  --api-cors-header=                   Set CORS headers in the remote API
  -b, --bridge=                        Attach containers to a network bridge
  --bip=                               Specify network bridge IP
  -D, --debug=false                    Enable debug mode
  -d, --daemon=false                   Enable daemon mode
  --default-ulimit=[]                  Set default ulimits for containers
  --dns=[]                             DNS server to use
  --dns-search=[]                      DNS search domains to use
  -e, --exec-driver=native             Exec driver to use
  --fixed-cidr=                        IPv4 subnet for fixed IPs
  --fixed-cidr-v6=                     IPv6 subnet for fixed IPs
  -G, --group=docker                   Group for the unix socket
  -g, --graph=/var/lib/docker          Root of the Docker runtime
  -H, --host=[]                        Daemon socket(s) to connect to
  -h, --help=false                     Print usage
  --icc=true                           Enable inter-container communication
  --insecure-registry=[]               Enable insecure registry communication
  --ip=0.0.0.0                         Default IP when binding container ports
  --ip-forward=true                    Enable net.ipv4.ip_forward
  --ip-masq=true                       Enable IP masquerading
  --iptables=true                      Enable addition of iptables rules
  --ipv6=false                         Enable IPv6 networking
  -l, --log-level=info                 Set the logging level
  --label=[]                           Set key=value labels to the daemon
  --log-driver=json-file               Containers logging driver
  --mtu=0                              Set the containers network MTU
  -p, --pidfile=/var/run/docker.pid    Path to use for daemon PID file
  --registry-mirror=[]                 Preferred Docker registry mirror
  -s, --storage-driver=                Storage driver to use
  --selinux-enabled=false              Enable selinux support
  --storage-opt=[]                     Set storage driver options
  --tls=false                          Use TLS; implied by --tlsverify
  --tlscacert=~/.docker/ca.pem         Trust certs signed only by this CA
  --tlscert=~/.docker/cert.pem         Path to TLS certificate file
  --tlskey=~/.docker/key.pem           Path to TLS key file
  --tlsverify=false                    Use TLS and verify the remote
  -v, --version=false                  Print version information and quit

Commands:
    attach    Attach to a running container
    build     Build an image from a Dockerfile
    commit    Create a new image from a container's changes
    cp        Copy files/folders from a container's filesystem to the host path
    create    Create a new container
    diff      Inspect changes on a container's filesystem
    events    Get real time events from the server
    exec      Run a command in a running container
    export    Stream the contents of a container as a tar archive
    history   Show the history of an image
    images    List images
    import    Create a new filesystem image from the contents of a tarball
    info      Display system-wide information
    inspect   Return low-level information on a container or image
    kill      Kill a running container
    load      Load an image from a tar archive
    login     Register or log in to a Docker registry server
    logout    Log out from a Docker registry server
    logs      Fetch the logs of a container
    port      Lookup the public-facing port that is NAT-ed to PRIVATE_PORT
    pause     Pause all processes within a container
    ps        List containers
    pull      Pull an image or a repository from a Docker registry server
    push      Push an image or a repository to a Docker registry server
    rename    Rename an existing container
    restart   Restart a running container
    rm        Remove one or more containers
    rmi       Remove one or more images
    run       Run a command in a new container
    save      Save an image to a tar archive
    search    Search for an image on the Docker Hub
    start     Start a stopped container
    stats     Display a stream of a containers' resource usage statistics
    stop      Stop a running container
    tag       Tag an image into a repository
    top       Lookup the running processes of a container
    unpause   Unpause a paused container
    version   Show the Docker version information
    wait      Block until a container stops, then print its exit code

Run 'docker COMMAND --help' for more information on a command.

``` 

# 开始新建一个docker容器

在docker镜像市场上找到需要下载的镜像，进行下载
``` bash
docker pull ubuntu:14.04
``` 
下载镜像的过程可能比较慢，镜像大小有差不多200MB

下载完成之后，可以查看本地拥有的镜像
``` bash
root@ubuntu:/# docker images 
REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
ubuntu              14.04               d4751aa1c40a        5 days ago          188 MB
``` 
之后就可以开始创建容器了
``` bash
root@ubuntu:/# docker run --net=host -it ubuntu:14.04
root@c944a54f35dc:/# 
``` 
创建成功

但是发现ping 8.8.8.8失败
``` bash
root@c944a54f35dc:/# ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
From 172.17.0.1 icmp_seq=1 Destination Host Unreachable
From 172.17.0.1 icmp_seq=2 Destination Host Unreachable
``` 
根据stackover找到的答案
I was facing the same problem. So, to solve that issue I've started the container using the argument --net=host, it worked perfectly for me.

Here goes the full statement

`sudo docker start --net=host -it --name ex_ngninx ubuntu`

This made it temporarily work but I still couldn't docker build. A `sudo systemctl restart docker` fixed it tho – Freedom_Ben Jan 24 at 22:41

所以遇到不能上网的情况可以通过`sudo systemctl restart docker`解决

``` bash
root@5acfffe415ce:/# exit
exit
root@ubuntu:/home/jwl# docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                     PORTS               NAMES
5acfffe415ce        ubuntu:14.04        "/bin/bash"         15 seconds ago      Exited (0) 5 seconds ago                       dreamy_feynman      
root@ubuntu:/home/jwl# sudo systemctl restart docker
root@ubuntu:/home/jwl# docker start 5acff
5acff
root@ubuntu:/home/jwl# docker exec -it 5acff bash
root@5acfffe415ce:/# ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=127 time=28.2 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=127 time=27.0 ms
``` 
成功

