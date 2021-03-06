# Consul Cluster Test Suite

A proof-of-concept which aims to recreate a distributed cluster system of [Consul](http://www.consul.io/) servers in order to test multiple failure points, service isolation and network errors reproducing various complex scenarios to verify the system reilabiity and non-blocking cluster fault tolerance with a successful service health and behavior

That's possible thanks to [Docker](https://docker.io) [containers](https://registry.hub.docker.com/u/innotech/consul/) and [Blockade](http://blockade.readthedocs.org/en/latest/), which is a Python utility to easily infer multiple network latencies, failures and isolation between Docker containers using [tc](http://en.wikipedia.org/wiki/Tc_%28Linux%29) and [iptables](http://en.wikipedia.org/wiki/Iptables) in the Docker host

It's planned to setup and test multiple cluster and failure scenarios using [Lettuce](http://lettuce.it/) for a BDD-style approach

## Requirements

- **Python** 2.7+ with [pip](https://pypi.python.org/pypi/pip) package manager
- **VirtualBox** 4.0+
- **Vagrant** 1.6+

<!--
- **Docker** 1.0+ (see [boot2docker](http://boot2docker.io/) for OSX)
-->

## Setup

Clone this repository
```bash
git clone https://github.com/innotech/consul-cluster-test && cd consul-cluster-test
```

Start the VM (this may take a while the first time)
```bash
vagrant up
```

Enter the VM
```bash
vagrant ssh
```

Go the vagrant shared directory
```bash
cd /vagrant
```

Install Python dependencies via `pip`
```bash
$ sudo pip install -r requirements.txt
```

Run Consul cluster using [Blockade](https://github.com/dcm-oss/blockade/blob/master/docs/guide.rst)
```bash
sudo blockade up
```

Use the help flag to see all the available commands
```bash
$ blockade --help
usage: blockade [-h] [--config blockade.yaml]
                {up,destroy,status,logs,flaky,slow,fast,partition,join} ...

Blockade

optional arguments:
  -h, --help            show this help message and exit
  --config blockade.yaml, -c blockade.yaml
                        Config YAML. Looks in CWD if not specified.

commands:
  {up,destroy,status,logs,flaky,slow,fast,partition,join}
```

## Testing

Run acceptance tests (still a work in progress)
```bash
$ make test
```

### Docker debugging

Create and use the bash shell inside the container
```bash
sudo docker run -i -t innotech/consul:0.4.1 /bin/bash
```

Or enter in a running container

```bash
sudo docker exec -it <containerIdOrName> bash
```

### Blockade testing

See the [complete guide](http://blockade.readthedocs.org/en/latest/guide.html) for more information

The cluster info is defined in `blockade.yml`

##### Infer high latency in network packets

```bash
sudo blockade slow c2
```

For more information how to configure a Consul cluster, see the [documentation](http://www.consul.io/intro/getting-started/join.html)

##### Infer a flaky latency in network packets

```bash
sudo blockade flaky c3
```

##### Partition the network between containers

```bash
sudo blockade partition c1,c3 c2
```

##### Join them again

```bash
sudo blockade join
```

##### Verify the status
```bash
sudo blockade status
```

##### Destroy and clean up

```bash
sudo blockade destroy
```

##### See the logs of a container

```bash
sudo blockade logs c3 | tail
```

## Docker usage

### Build a new image

```bash
$ sudo docker build -t innotech/consul:<version> .
```

### Release new version

```bash
$ sudo docker push innotech/consul:<version>
```

See `Makefile` for additional command shortcuts

## License

MIT - Innotech (c) Copyright
