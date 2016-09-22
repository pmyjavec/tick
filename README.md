# TICK Stack

The TICK stack from InfluxData is full end-to-end solution for collecting, storing, visualising and alerting on time-series data. The platform is highly scalable and completely open-source. The name TICK is an acronym and is  derived from the components which make up the stack, Telegraf, InfluxDB, Chronograf, Kapacitor. See more at [InfluxData] (https://www.influxdata.com/use-cases/introducing-the-tick-stack/).

This repository contains [Ansible Playbooks](https://docs.ansible.com/ansible/playbooks_intro.html) for deploying the TICK stack to an Ubuntu Linux host and could easily be extended for deploying to much larger production environments in the cloud.

Ansible is a fantastic configuration management and orchestration tool, it's not only very easy to learn, it's self documenting. This means that our documentation is not only useful for learning how to deploy and configure the stack, but actually automates tasks for us, delivering an easy to reproduce deployment to hack and extend.

## Components

All components of the stack are fully open-source, and written in Go. Source code for all components can be found on the GitHub site [InfluxData](https://github.com/influxdata)

These components working together give us a stack which not only collects and stores time-series data, but provides alerting and visualisation functions.

### InfluxDB

A horizontally scalable, open source, high performance time-series database written entirely in the Go.  Metrics collected from _Telgraf_ are stored in InfluxDB, data can be pushed to external systems such as _Kapacitor_ for stream processing and alerting.

InfluxDB also exposes a REST API for easily writing, retrieving, subscribing to metrics, for more see the [docs](https://docs.influxdata.com/influxdb/v1.0/)

### Telegraf

Telegraf is a lightweight agent used to collect time-series data, it's written entirely and Go and in the case of the TICK stack, it's used to report metrics directly to an InfluxDB cluster, it can be used to report metrics to other systems.

Telegraf comes with a flexible plug-in system and is great for collecting system metrics, in our case CPU metrics. It has a flexible plug-in system and can be used to collect data from an array of other sources.

### Kapacitor

Kapacitor can process batches and or streams of time-series data using a DSL known as TICK script, this processed data can be used to trigger alerts and identify anomalies in received samples. In our case, we use it to generate alerts when CPU usage is high but it can do a lot more than just that.

It's important to realise that Kapacitor _subscribes_ to time-series data streams send from InfluxDB, checkout the docs for more on that.

For more on what Kapacitor can do checkout the [getting started guide](https://docs.influxdata.com/kapacitor/v1.0/introduction/getting_started/)

### Chronograf

Is used to nicely visualize data stored in InfluxDB. Currently because we use Telegraf to report CPU usage to InfluxDB, it's a nice way to display this information in pretty graphs. It's not a vital piece of the stack, alerts would still work without this piece.

## Requirements

### Vagrant

As stated earlier, we use Ansible for deploying the stack, but Vagrant provides us with a place, or virtual machine, to actually deploy the stack, in a reproducible, automated way.

Installation instructions can be found on the [Vagrant website](https://www.vagrantup.com/docs/installation/)

### Ansible

Ansible is a modern day configuration management and orchestration tool in which the configuration management code is considered to be self-documenting .

Please see the [Ansible installation guide](http://docs.ansible.com/ansible/intro_installation.html<Paste>)

## Getting started

The first thing we should do is deploy the stack, using our fully automated process, at first it may seem like magic; however, paying close attention to the output will literally walk us through the deployment procedure, outlining  exactly the tasks being performed to deploy the stack. Go ahead and run the `vagrant up` command from this directory.

This step make take some time as Vagrant will perform the following steps:

1. Parse the `Vagrantfile`, and setup the environment accordingly.
1. Download the vagrant machine image as specified in the `Vagrantfile`.
2. Boot the Linux VM.
3. Run Ansible playbook, `tick.yml` against the VM, installing the entire TICK stack. This happens via SSH connection, see the Vagrant doc for more details.

Once `vagrant up` is complete you will be able to access the _Chronograph_ dashboard in your browser by visiting `http://localhost:10000`

The key to all of this is in the `Vagrantfile`, it's the entry point for the deployment, used by Vagrant and runs Ansible on our behalf. Go ahead and check it out, it is heavily commented and each section is self-explanatory.

Next is the `tick.yml` playbook, this is the entry-point for Ansible. Each section of the file is documented and is also quite self-explanatory.

Now everything is running we can login to the box and have a play around with the environment by running `vagrant ssh`, you will see all the components running and may experiment with it, or use the playbooks to deploy to a production environment.

## Working with the stack

Now the stack is deployed we can configure our alerts and change the configuration as required.

### Creating alerts

Once the deployment is completed, we still need to configure an alert, this step has not been easy to automate and is still performed manually. The following procure can be followed to alert when the CPU is utilized more than 70%.

```
cat > cpu_alert.tick
// Example of the TICK DSL in-action
stream
    // Select just the cpu measurement from our example database.
    |from()
        .measurement('cpu')
    |alert()
        .crit(lambda: "usage_idle" <  70)
        // Whenever we get an alert write it to a file.
        .log('/tmp/alerts.log')
```

Activate the alerts, this CLI persists settings by communicating with the REST API of Kapacitor, or `kapacitord`.

```
$ kapacitor define cpu_alert -type stream -tick cpu_alert.tick -dbrp telegraf.autogen
$ kapacitor enable cpu_alert
# Generate traffic and watch alerts, don't do this in production :)
$ cat /dev/urandom | gzip | gzip -9 > /dev/null &
$ tail -f /tmo/alerts.log
```

### Teardown

When finished with your local environment, just run `vagrant destroy`

## Getting help

All components used are open source, if Vagrant or Ansible fails, you can join the respective IRC channels on Freenode and ask for help.

## TODO

* Ensure latest versions are installed by default. For example, avoid having to set variables in the `tick.yml`
* Set up a continuous query to calculate and store the average CPU busy percentage every 30 minutes.
* From Vagrant to production deployments ! :)
