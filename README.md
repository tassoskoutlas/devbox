# Devbox - Virtual LAMP server

## Prerequisites

Devbox is a virtual devevelopment box for PHP and JavaScruipt development
(including frameworks like Hugo, Symfony, WordPress and Drupal). Using the LAMP
stack provided you can start development without worrying about setting up your
computer as a development environment.

Devbox works with the following software:

  - Vagrant >= 2.1.1
  - Ansible >= 2.5
  - VirtualBox
  - Ubuntu Xenial 16.04

Devbox assumes that projects are developed with Git and requires a valid RSA key
from the local user which has been added in Github.

## Install

Get devbox in your local machine:

```
git clone git@github.com:ioa-maellak/devbox.git && cd devbox
```

Create the configuration file to configure the development environment:

```
cp default.config.yml config.yml
```

Once done editing `config.yml` run Vagrant to create the development
environment:

```
vagrant up
```

## Usage

You can login to the devbox with the following command:

```
vagrant ssh
```

For changes that affect the operation of the server in `config.yml` you need
to reprovision the environment (e.g. when adding new projects):

```
vagrant provision
```

For changes that the virtual machine in `config.yml` you need to reload the
configuration (e.g. change the IP address):

```
varnish reload
```

To access vagrant help:

```
vagrant help
```

## Configuration

The development environment can be customised via the `config.yml` file and the
variables contained within. 

### Configuring a remote user

The `remote_user` variable is used to set the user in the guest machine. The
default user in Vagrant is ubuntu and is reccomended to be left as is.

### Configuring Git, an RSA key and repositories

The git configuration section contains variables that control the configuration
of Git and RSA keys. You can control how your local key is named in devbox so
you can add your development key indepedently of any other key already set.

The repositories configuration section contains a variable that is used to setup
the devbox `~/.ssh/config` file.

### Configuring the server stack

The `config_roles` variable is used to customise the software stack configured
in the server. Comment out roles not needed.

### Configuring your projects

Vagrant allows to share directories between the host and the guest
systems. Devbox takes advantage of that feature to expose local projects which
can then be served through the LAMP stack.

Devbox expects a `projects` folder to exist in the same contains directory with
devbox:

```
.
├── devbox
└── projects
   ├── projectA
   ├── projectB
   └── projectC
```

All directories contained withing projects will be exposed in `~/projects` in
the guest system.

For each project the following parameters can be set:

- domain (string): The URI to access project.
- docroot (string) The path of the project in devbox (user `{{ remote_user }}`
  for indepedence
- db_user (string): The name of the database user to be used (root will be used
  if empty)
- db_pass (string): The passwrd of the database user to be used (root will be used if empty)
- db_name (string): The database name to be used (if empty database creation is skipped)
- tech (string): The technology used in the project. Options include:
    - hugo
	- drupal
	- symfony

Each project has an associated domain which is being automatically discovered by
vagrant and added to your `/etc/hosts`.

### Configuring additional software to be added

The `additional_packages` variable allows additional software to be included in
the main build. It is intended for smaller packages that do not require
customisation. If more software with significant customisation is needed
consider to open a pull request.

### Configuring Vagrant

Devbox offers a complete development environment out of the box. Vagrant is
controled through a `Vagrantfile` bunlded at the root of the repository. Within
that file there are parameters about:

  - vagrant options about the base image, synchronising folders, network, etc
  - virtual machine options about memory, etc
  - provision options about ansible

Please follow through [vagrant documentation](https://www.vagrantup.com/docs/)
for a more thorough understanding of how it can be tweaked.

## Contributions

Everyone is welcome to contribute. The most established way to do so in github
is to fork a project and create a pull request. Read this post on
[how to use pull requests on github](https://help.github.com/articles/using-pull-requests/).

## Credits

This code is developed and maintained by
[Tassos Koutlas](https://github.com/tassoskoutlas) for the Ioannina FOSS Unit of
Excellence ([ioa-maellak](https://github.com/ioa-maellak/)).

The code is distributed under the
[EUPL v1.1](http://ec.europa.eu/idabc/eupl.html) open source software license.
