# Running Existing Tasks

> **Difficulty**: Intermediate

> **Time**: Approximately 10 minutes

In this exercise you will explore some existing tasks, including several tasks that take advantage of Puppet under-the-hood.

> **Note:** Some of the following content will be available on The Forge soon.

- [Install Puppet using Bolt](#install-puppet-using-bolt)
- [Use package task to check status of package](#use-package-task-to-check-status-of-package)
- [Use package task to install a package](#use-package-task-to-install-a-package)
- [The Tasks Playground](#more-tips-tricks-and-ideas-on-the-tasks-playground)

# Prerequisites

For the following exercises you should already have `bolt` installed and have a few nodes (either Windows or Linux) available to run commands against. The following guides will help:

1. [Installing Bolt](../1-installing-bolt)
1. [Acquiring nodes](../2-acquiring-nodes)

It is also useful to have some familiarity with running commands with `bolt` so you understand passing nodes and credentials. The following exercise is recommended:

1. [Running Commands](../3-running-commands)

# Clone the control repo and configure Bolt's modulepath

These exercises will use the [task-modules](https://github.com/puppetlabs/task-modules) control repo. Like many control repos this repository contains some modules committed directly in the `site` directory and manages other with a `Puppetfile`.

```
mkdir ~/.puppetlabs
cd ~/.puppetlabs
git clone git@github.com:puppetlabs/task-modules.git
```

Now open `~/.puppetlabs/bolt.yml` and set up the bolt module path. The module path will include both modules commited directly in `site` and those managed by the puppetfile in `modules`.

```
---
modulepath: "~/.puppetlabs/task-modules/site:~/.puppetlabs/task-modules/modules"
# If you have to pass --insecure to skip host key verifaction you can
# uncomment these lines.
#ssh:
#  insecure: true
```

# Install Puppet using Bolt

The [`install_puppet` task](https://github.com/puppetlabs/task-modules/blob/master/site/install_puppet/tasks/init.sh) in task-modules contains a task to install the puppet agent package on a node. This task need to run as root so if you're not logging in as root with vagrant you'll need to tell bolt to sudo to root with `--run-as root`

```
bolt task run install_puppet -n node1 --run_as root
```

This task may take a while and will produce a lot of output when it's done.

# Install r10k and Puppetfile modules

Committing modules directly to the control repo is useful while developing new modules or for private modules that won't be shared. For public modules hosted either in their own repositories on the Puppet Forge it's easier to use a Puppetfile and install them with r10k. To do that first you need to install `r10k`

```
gem install r10k
```

Now you can use r10k to install the modules in the Puppetfile `~/.puppetlabs/task-modules/Puppetfile`

```
cd ~/.puppetlabs/task-modules
r10k puppetfile install ./Puppetfile
```

# Use package task to check status of package

With Puppet installed on the node we can use some of the tasks that expose Puppet resources, like the package task which you just installed from the Puppet Forge with r10k.

Let's quickly check on the status of a specific package using `bolt`:

```
$ bolt task run package action=status name=bash --nodes node1
Started on node1...
Finished on node1:
  {
    "status": "up to date",
    "version": "4.2.46-29.el7_4"
  }
Ran on 1 node in 3.81 seconds
```

# Use package task to install a package

The package task also supports other actions, including ensuring a package is installed. Let's install a package across all of our nodes using that action:

```
$ bolt task run package action=install name=vim --nodes node --run-as root
Started on node1...
Finished on node1:
  {
    "status": "present",
    "version": "2:7.4.160-2.el7"
  }
Ran on 1 node in 15.26 seconds
```

# More tips, tricks and ideas on the Tasks Playground

We've really only scratched the surface of Tasks in these exercises. You'll find lots more tips, tricks, examples and hacks on the [Puppet Tasks Playground](https://github.com/puppetlabs/tasks-playground).

# Next steps

Now you know how to download and run third party tasks with `bolt` you can move on to:

1. [Writing Plans](../7-writing-plans)
