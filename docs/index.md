# Introduction

!!! missing still work in progress. More to come

As a software developer or even as a researcher you may sometime end up in situations
where you feel the need of a data center on your laptop either to develop or run more
complex software systems, or just occasionally need to spin up applications you really
want to isolate. If you also need to share your setup with others or just want to work
in a systematic and reproducible way this repo documents a simple [**desire path**](https://en.wikipedia.org/wiki/Desire_path) solution based on a small collection of standard and (somewhat) mature software projects,
[Ansible](https://docs.ansible.com/ansible/latest/index.html),
[Molecule](https://ansible.readthedocs.io/projects/molecule/),
[Vagrant](https://developer.hashicorp.com/vagrant/docs), and
[libvirt](https://libvirt.org/manpages/libvirtd.html)
In addition, the repo can also be used to aggregate software and documentation from
multiple independent repos as git submodules.


## Quickstart

  1. [Prepare your system](#installation)
  2. Clone the repo
  3. Choose a project
  4. Fetch an image
  5. Create a scenario
  6. Converge the scenario

Create fedora 35 kvm vm using the molecule-libvirt driver to test a
project running with the molecule-podman driver.


         $ git clone ..
         $ cd molecular
         $ ./bin/install.sh libvirt         # install rpm and python packages
         $ ./bin/get fedora35               # fetches a fedora 35 qcow2 image
         $ molecule create -s vm            # creates the fedora 35 vm
         $ molecule converge -s vm          # run project tasks inside the vm
         $ molecule list
         $ molecule login -s vm             # login into the vm
         $
         $     git clone ..                 # clone inside the vm
         $     cd molecular
         $     ./bin/install.sh podman      # install rpm and python packages
         $     molecule create -s podman    # create the container inside the vm
         $     molecule converge -s podman  # run project tasks inside the container
         $     molecule list
         $     molecule login -s podman     # login into the container


## Usage

  1. Create a demo project from the molecule template

         $ cd molecular/projects
         $ cookiecutter --config-file ../templates/molecule

  2. Get images and start the web server

          $ make hole
          $ make it

  3. Create new demo scenario from scratch,

          $ demo/molelcule
          $ molecule init -s scenario -d ...

      or use the cookiecutter scenario template for pre-made scenarios,

          $ cd molecular/projects/demo/molecule
          $ cookiecutter ../../../templates/scenario

  4. Use molecule according to the documentation,

          $ cd projects/molecular
          $ molecule list
          $ molecule create   -s demo
          $ molecule login    -s demo
          $ molecule converge -s demo

   5. Clone an existing multi-vm molecule for a specific SSH certificate scenario setup

          $ cd molecular/projects
          $ git clone https://github.com/lmelwyn/vm.sshca

   6. Add the documentation of a molecule to the molecular MkDocs by
      placing a symlink in _molecular/docs_, _e.g._,

          $ cd molecular/docs
          $ ln -s ../projects/molecule_name/docs

Here, we partly abuse molecule beyond its intended use and may occasionally have to resort to hacks to achieve
a specific outcome. One example is to apply vagrant commands, such as **vagrant up / down**,
directly in the instances vagrant environment placed cachedirs under _~/.cache/molecule_.


## Installation

Clone the repository,

          $ git clone https://github.com/lmelwyn/molecular.git

and install all the dependencies using the ansible playbook _install.yml_,

          $ ansible-playbook --ask-become-pass install.yml

Alternatively, do a manual installation of the distro and python packages,

  1. install vagrant and libvirt distro packages

          $ dnf install libvirt vagrant vagrant-libvirt

     or

          $ apt-get install libvirt vagrant vagrant-libvirt

  2. Install the requirements, _i.e._, Ansible and Molecule

          $ cd molecular
          $ pip install -r requirements.txt

     or

          $ pip install --user ansible ansible-core ansible-compat ansible-lint
          $ pip install --user molecule molecule-plugins['podman','vagrant'] python-vagrant
          $ pop install --user cookiecutter mkdocs mkdocs-material

  3. Fetch latest vagrant base VM images

          $ vagrant box add rockylinux/8
          $ vagrant box add rockylinux/9
          $ vagrant box add almalinux/8
          $ vagrant box add almalinux/9
          $ vagrant box add fedora/38-cloud-base
          $ vagrant box add alpine/alpine64
          $ vagrant box list



## Background

Ansible is a configuration management and orchestration tool. It allows you to define your infrastructure as code,
and manage and automate the configuration and deployment of systems. Ansible uses a simple, descriptive, and human-readable
language to describe your infrastructure and actions, and can be used to manage a wide range of systems, including servers,
networks, and cloud environments.

More generally Ansible may also be used as a workflow system where complex workflows can be created
by organizing tasks into playbooks (executable recipes). The playbooks can then be run manually
or automatically as a part of a CI/CD pipeline, allowing the automation of complex processes
which ensure consistent execution and reproducibility. Additionally, Ansible's integration
with other tools, such as Gitlab, allows you to incorporate Ansible into your continuous integration
and deployment (CI/CD) pipelines. This can help you automate and manage complete reproducible workflows
of various kinds.

There is a large ecosystem build around ansible and many tools focus on the reuse and sharing of code. The
[Ansible Galaxy](https://galaxy.ansible.com) portal contains a huge amount of readily available open source
playbooks, roles and more generally collections.

The [Ansible Molecule](https://molecule.readthedocs.io/en/latest/) Python package is designed to
aid in the of development and testing of Ansible roles and playbooks for configuration management
and system orchestration. It is an easy and fast way to create complex multi-node test or proof-of-concept
scenarios that works well with both containers and virtual machines, but Molecule may also be slightly
upcycled as minimal container or virtual machine manager for personal projects to make build, runtime or
data analysis workflows in containers or virtual machines.

Historically Molecule came with a number of [drivers](https://github.com/topics/molecule-driver), but recently
a limited number have been moved to the ansible-community developed [Molecule Plugins](https://github.com/ansible-community/molecule-plugins)
repository. Here we mainly use the podman and vagrant (with libvirt) plugins for user space containers or
KVM virtual machines.

There are other CLI based frameworks for managing virtual machines, such as
[virt-lightning](https://github.com/virt-lightning/virt-lightning) or just the libvirt
provided [virsh](https://www.libvirt.org/manpages/virsh.html), but molecule with the
vagrant plugin just happens be to one that is well-integrated with Ansible and is quite
simple to use in cases where certain level of isolation is required or different OS is needed.

In more advanced cases [nested virtualization](https://www.linux-kvm.org/page/Nested_Guests)
allows for development and test of virtualized molecules, _e.g._, see the __molecule.minione__ repo.

Cookiecutter templates are provided for __fast__ configuration of new molecules and scenarios.
The molecule cookiecutter template initializes a MkDocs site per molecule for the documentation
of the molecule.

## Links

  * [Ansible](https://docs.ansible.com/projects/ansible/latest/index.html)
      - [Roles](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_reuse_roles.html)
      - [Collections](https://docs.ansible.com/ansible/latest/collections_guide/index.html)
  * [Ansible Galaxy](https://galaxy.ansible.com/docs/)
      - [Roles](https://galaxy.ansible.com/docs/contributing/creating_role.html)
      - [Collections](https://galaxy.ansible.com/docs/contributing/creating_collections.html)
  * [Libvirt](https://libvirt.org)
  * [Vagrant](https://developer.hashicorp.com/vagrant/docs)
      - [Vagrant libvirt](https://vagrant-libvirt.github.io/vagrant-libvirt/)
  * [Molecule](https://ansible.readthedocs.io/projects/molecule/)
      - [Molecule plugins](https://github.com/ansible-community/molecule-plugins)
  * [Cookiecutter](https://pypi.org/project/cookiecutter/)
  * [MkDocs](https://www.mkdocs.org/)
      - [Material theme](https://squidfunk.github.io/mkdocs-material/)
