This is my back-end api for a distributed, replicated, fault tolerant, causally consistent, and sharded key-value store.
The system is distributed among shards that contain at least two replicas on each shard to maintain fault-tolerance invariant.
The keys are distributed to the shards using a hash function mod the number of shards to ensure that the keys
are evenly distributed among the system.
A vector clock is used to check for causally consistent writes from the client and other nodes.
Broadcast alogirthms replicate key values among the nodes on the same shard.

# Distributed Key Value Store

To get started, you might want to explore the project directory structure and browse the modularized functions.

The project is ran on Docker containers, so in order to properly create the nodes and distribute them among shards you must have Docker installed.

To try out the key-value store comprised of shards of nodes, you will need to construct a cluster of your own. In this section we’ll construct the following scenario:

• 3:3 – Alice, Bob, and Carol in one shard. Dave, Erin, and Frank in another.

Nodes will have subnet addresses from the range 10.10.0.2 to 10.10.0.7, but all
will listen on container port 8090. Nodes will have their container port 8090
published on host ports from the range 8082 to 8087. On startup each node will be
provided with the environment variables:

• SOCKET_ADDRESS is a string in the format "IP:PORT" describing the current node.
• VIEW is a comma-delimited string containing the socket addresses of all the running nodes. • SHARD_COUNT is the number of shards to divide nodes (and keys) into.

Initial setup
• Build your container image and tag it asg4img: $ docker build -t asg4img .
• Create a subnet called asg4net with IP range 10.10.0.0/16: $ docker network create --subnet=10.10.0.0/16 asg4net
Run instances in the network
Run each of the nodes in the subnet:
$ docker run --rm -p 8082:8090 --net=asg4net --ip=10.10.0.2 --name=alice -e=SHARD_COUNT=2
    -e=SOCKET_ADDRESS=10.10.0.2:8090
    -e=VIEW=10.10.0.2:8090,10.10.0.3:8090,10.10.0.4:8090,10.10.0.5:8090,10.10.0.6:8090,10.10.0.7:8090
    asg4img
$ docker run --rm -p 8083:8090 --net=asg4net --ip=10.10.0.3 --name=bob   -e=SHARD_COUNT=2
    -e=SOCKET_ADDRESS=10.10.0.3:8090
    -e=VIEW=10.10.0.2:8090,10.10.0.3:8090,10.10.0.4:8090,10.10.0.5:8090,10.10.0.6:8090,10.10.0.7:8090
    asg4img
$ docker run --rm -p 8084:8090 --net=asg4net --ip=10.10.0.4 --name=carol -e=SHARD_COUNT=2
    -e=SOCKET_ADDRESS=10.10.0.4:8090
    -e=VIEW=10.10.0.2:8090,10.10.0.3:8090,10.10.0.4:8090,10.10.0.5:8090,10.10.0.6:8090,10.10.0.7:8090
    asg4img
$ docker run --rm -p 8085:8090 --net=asg4net --ip=10.10.0.5 --name=dave  -e=SHARD_COUNT=2
    -e=SOCKET_ADDRESS=10.10.0.5:8090
    -e=VIEW=10.10.0.2:8090,10.10.0.3:8090,10.10.0.4:8090,10.10.0.5:8090,10.10.0.6:8090,10.10.0.7:8090
    asg4img
$ docker run --rm -p 8086:8090 --net=asg4net --ip=10.10.0.6 --name=erin  -e=SHARD_COUNT=2
    -e=SOCKET_ADDRESS=10.10.0.6:8090
    -e=VIEW=10.10.0.2:8090,10.10.0.3:8090,10.10.0.4:8090,10.10.0.5:8090,10.10.0.6:8090,10.10.0.7:8090
    asg4img
$ docker run --rm -p 8087:8090 --net=asg4net --ip=10.10.0.7 --name=frank -e=SHARD_COUNT=2
    -e=SOCKET_ADDRESS=10.10.0.7:8090
    -e=VIEW=10.10.0.2:8090,10.10.0.3:8090,10.10.0.4:8090,10.10.0.5:8090,10.10.0.6:8090,10.10.0.7:8090
    asg4img
