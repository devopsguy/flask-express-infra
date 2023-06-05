# Flask and Express App Example Documentation

Here you will find the accompanying documentation for the infrastructure part of the Flask and Express app example.

Everything you need to get the example up and running for yourself.

## Prerequisites

You will need to install a few things first. I will assume you're running a macOS and you're using [`brew`](https://brew.sh/) as a package manager.

Minimal Prerequisites
+ [Docker Desktop](https://www.docker.com/products/docker-desktop/)
+ [minikube](https://minikube.sigs.k8s.io/docs/start/)
+ [kubectl](https://kubernetes.io/docs/reference/kubectl/)

Optional Prerequisites
+ [k9s](https://k9scli.io/)

Here's only one example for installing minikube using `brew`.

> `brew install minikube`

It's as simple as that.

## Setting up your computer

There is a little step you have to do prior to deploying your cluster and workloads. We will use an internal custom domain to access our services, which will be called [containers.test](http://containers.test)

For this we need to change our hosts file and add the necessary entries. First you need to find your cluster's IP address. So we need to get a cluster spun up.

### Creating a minikube cluster

To initialize and spin up a minikube cluster, all you need to do is issue one single command.

> `minikube start`

That's literally all you need to do. Now you wait for it to be up. We'll check it is ready by issuing the `minikube status` command.

> `minikube status`

It should tell you something similar to this.

> ```
> ➜  ~ minikube status
> minikube
> type: Control Plane
> host: Running
> kubelet: Running
> apiserver: Running
> kubeconfig: Configured
> ```

Now we need the IP address of our cluster, which we'll get using `minikube ip`.

> `minikube ip`

Copy that IP address to your clipboard and open your `/etc/hosts` file with your favorite text editor with root privileges to add the necessary entries.

This his how my hosts file looks like.

```
##
# Host Database
#
# localhost is used to configure the loopback interface
# when the system is booting.  Do not change this entry.
##
127.0.0.1	localhost
255.255.255.255	broadcasthost
::1             localhost
# Added by Docker Desktop
# To allow the same kube context to work on the host and the container:
127.0.0.1 kubernetes.docker.internal
# End of section
# Manual configuration
127.0.0.1 containers.test
192.168.49.2 containers.test, flask.containers.test, express.containers.test
```

Note the section below `# Manual configuration`. This is the part we've added. First a we let the loopback address point to _containers.test_ and then we let the IP address of the minikube cluster point to _containers.local_ and all relevant subdomains, in this case _flask.containers.test_ and _express.containers.test_.

Save and close that file.

Now we need to tell minikube to enable some addons we will use. We need the following:
+ ingress
+ ingress-dns

To enable those addons you use the - you've guessed it - `minikube addons` command.

> ```
> minikube addons enable ingress
> minikube addons enable ingress-dns
> ```

Now we'll simply apply our `ingress.yml` manifest in our local minikube cluster.

From within this respoitory you will run:

> `kubectl apply -f ingress.yml`

We've done setting up of machine. You will need to continue from the Flask and Express applications' repositories for proceed building the Docker images, publishing them to your docker registry and deploying them into your local cluster.