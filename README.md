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

## Basic usage

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

### Configuring the VM

The virtual machine can be automatically configured via the variables in
**Machine** and **Folder mapping**. There are a number of options that allow
devbox to operate in various conditions.

Folder mapping is achieved through NFS and bindfs to ensure that permisions are
set as expected. By default devbox is using 775 for directories and 644 for
files but behaviour can be overriden via `vm_folder_perm`.

### Configuring Git, an RSA key and repositories

The git configuration section contains variables that control the configuration
of Git and RSA keys. You can control how your local key is named in devbox so
you can add your development key indepedently of any other key already set.

The repositories configuration section contains a variable that is used to setup
the devbox `~/.ssh/config` file. Public key fingerprints can be used to validate
a connection to a remote server. Devbox automatically adds RSA fingerprints
in `~/.ssh/known_hosts` for the repositories declared.

### Configuring the server stack

The `config_roles` variable is used to customise the software stack configured
in the server. Comment out roles not needed.

### Configuring projects

Vagrant allows to share directories between the host and the guest
systems. Devbox takes advantage of that feature to expose local projects which
can then be served through the LAMP stack.

Devbox is configurable to act as a standalone server hosting many projects or as
a development environment within a particular project. In either case it expects
that the folder containing the project or projects to be declared via
`projects_path`.

Each project is configured via the `projects` dictionary which accepts the
following parameters:

  - **domain** (_string_): The URI to access project in the browser. The value is
  automatically picked up and added to `/etc/hosts`.
  - **docroot** (_string_) The path of the project in devbox. Can be any
    location or combined with `projects_path`.
  - **db_user** (_string_): The database user to be used. User is added if does
  not exist. Leave empty to use root.
  - **db_pass** (_string_): The database password for the user above. Leave empty to
    use root's password.
  - **db_name** (_string_): The database to be used. If empty database creation is skipped
  - **tech** (_string_): The technology used in the project. Options include:
    empty, hugo, drupal, wordpress, symfony.

### Configuring additional software to be added

The `additional_packages` variable allows additional software to be included in
the main build. It is intended for smaller packages that do not require
customisation. If more software with significant customisation is needed
consider to open a pull request.

### Configuring additional tasks to be performed

The `additional_tasks` variable allows additional tasks to be performed after
the server has been provisioned. This can be used for some adhoc configuration
like downloading emacs configuration specific to the user or adding custom
aliases to `.bashrc`.

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
