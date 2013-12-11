---
published: false
---

## Creating a Bare Vagrant Box with CentOS 6.4
Recently I have started using Vagrant and Ansible to build and configure different environments for the developers in my company. I wanted to start from scratch and create a Vagrant box for CentOS 6.4 using [VirtualBox](https://www.virtualbox.org), even though those packages already exist on [vagrantbox.es](http://www.vagrantbox.es/) so I understand how the process works.

There are quite a few tutorials out there, but I could not complete the process with just one of them, so I will create yet another tutorial, that has all of the steps I used to create a working CentOS 6.4 box. All sources will be linked at the end.

## Pre-requisites
You will need to get Vagrant and VirtualBox, as of this writing I am using Vagrant version 1.2.4 and VirtualBox 4.2.20 on MacOS X 10.9, so YMMV. Vagrant version 1.2.4 requires VirtualBox versions of 4.0, 4.1, or 4.2 to work.

## Get CentOS 6.4
I used the minimal install ISO for CentOS, which can be retrieved from one of the [CentOS mirrors](http://www.centos.org/modules/tinycontent/index.php?id=30).
