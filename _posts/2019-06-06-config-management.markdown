# Configuration Management for Beginners
## Using Chef as an Example

### Who is this blog post meant for?

This post is aimed at software engineers who have used configuration management software (especially Chef) without necessarily being comfortable with the process or fully understanding what they were doing. I spend a lot of time working with Chef in my day job, and frequently explain what's happening behind the scenes to software engineers in other sections of the engineering org who rarely make Chef changes. This blog post is a distillation of how I normally explain what configuration changes they're making and why.

### What is configuration management?

Configuration management is the process of creating and maintaining consistent state across a complex system. If you're curious, there's a more comprehensive definition and a long list of industries that use configuration management on [the Wikipedia page](https://en.wikipedia.org/wiki/Configuration_management).

### Why does configuration management software exist?

Say you have 1000 hosts that perform different functions. Some are web servers, some are mail servers, some host your database. You want some software on all of them: perhaps git, or monitoring software. And you want that software to be configured similarly on all of the hosts as well. For example, to ensure that the version of git is the same on every host, or that your LDAP configuration is the same everywhere.

In addition, you want some software on just some of your hosts. Of course you'd want a web server (Apache, Nginx, something else) installed on your web server hosts. You'll need a mail server for the mail server hosts, and a database on your database hosts. And again, you'd want the configuration for all of _those_ pieces of software to also be consistent. Configuration management software allows you to manage all this configuration from one central location, rather than logging onto each individual host to set this up manually yourself. It allows you to start thinking of your hosts as a fleet and manage them as such.

### So how does Chef do this?

A Chef repo has four major parts: data bags, recipes, roles, and environments.

#### Data Bags

Data bags are the simplest part; they're just files you can fill with whatever JSON you want to have access to on a host (typically called a node in Chef parlance). Data bags are organized into separate subdirectories depending on what type of data they contain. These organizational lines are entirely human-defined, so you can create whatever sort of subdirectory organization makes the most sense to you. Because data bags are so freeform, people put all sorts of things in them. [Here's a great blog post](https://blog.dnsimple.com/2017/05/databag_refactor/) by DNSimple where they use a data bag to store network config.

#### Recipes

A recipe is an individual script written in a Ruby DSL ([Domain-specific language](https://en.wikipedia.org/wiki/Domain-specific_language)). A recipe can include a variety of steps, such as installing a software package or installing a file from a template. Steps in a recipe are attempted in order. Because Chef recipes are just Ruby code with some syntactic sugar, you can include logic within your recipe to only perform certain actions if the node has certain attributes, such as if it has a specific version of your operating system. Additionally, you can create your own attributes and attach them to certain nodes to use like tags. You can also consult the information contained in data bags from recipes.

Recipes are collected into cookbooks, which, like the data bags, are purely for human readability. However, cookbooks are generally organized along software lines. So for example, you might have a MySQL cookbook, where all the recipes contained within the cookbook relate to the installation and configuration of MySQL. You can also download _community cookbooks_, cookbooks created by other people, on the [Supermarket](https://supermarket.chef.io/). A cookbook should always have a "default" recipe. It may also have other recipes for installing the same piece of software but a different version, or be customized for a specific type of host, etc.

Cookbooks are versioned within Chef. When you make changes to a recipe, you must bump the respective cookbook version and push the new cookbook version to the Chef server.

#### Roles

A role is a functional collection of recipes that are all needed to build a specific type of host. Roles can contain other roles, so you can choose to create a base role as you would a base image, and add that base role to every new role you create from then on. A role executes all roles and recipes listed within it in order, which can cause ordering problems as your system grows in complexity and more roles and recipes are added.

#### Environments

An environment file is essentially a long list of key/value pairs where the key is a cookbook name and the value is a version number. After editing a cookbook and bumping the cookbook's version number, you must update the corresponding version number in any relevant environment files. This tells Chef to actually start using the new cookbook version in all roles that include it. 

Why would you have different environment files? Environment files allow you to build different ecosystems, because you can have environment files with different cookbooks or even different versions of the same cookbook. For example, you might have different environment files for different deployment environments, such as one for staging and one for production. Or maybe you have one environment file that acts as staging for infrastructure changes themselves, and after making changes to a file in a cookbook you update the cookbook in your infrastructure staging environment file before rolling out the same cookbook version to your production systems.

### Okay, but how do my changes actually take effect?

Chef has two functional parts. The Chef server stores all of your node and repo data. When you upload cookbook changes, for example, this is where those changes go. The other major part of Chef is chef-client, the daemon that sits on every node and regularly checks into the Chef server to see if there are any cookbook updates relevant to that node. For any updated cookbooks, chef-client will diff the changes between the old version and the new version, and then try to implement the diff. In other words, with Chef, you can make incremental updates to some or all hosts at the same time.

### Some related concepts:
### What are some alternatives to Chef?

There are two other common alternatives to Chef: [Puppet](http://www.puppet.com/) and [Ansible](https://www.ansible.com). You can find a full list of configuration management software [on Wikipedia](https://en.wikipedia.org/wiki/Comparison_of_open-source_configuration_management_software).

#### Infrastructure as Code, or Configuration as Code

Exactly what it sounds like! The saving of your configuration, from the creation of your instances in the first place if you're in the cloud (how much memory do they have? how much disk space?) to the software installed on them (which image are you using for this host? what operating system? what software config?), as code in a repository rather than ephemeral values that you edit by hand. Config as code allows you to use version control software to manage it, giving you all the benefits that come with VCS, such as being able to roll back to older versions of the code and tracking who's made what changes to the code and why. [The Wikipedia page on Infrastructure as Code](https://en.wikipedia.org/wiki/Infrastructure_as_code) is great if you're looking for more info on this concept.

### Mutable vs. Immutable Infrastructure

Mutable infrastructure describes a system where you can modify existing resources, such as Chef and other configuration management tools (see above). In Chef, you can make changes to already-existing hosts by adding new steps to existing recipes, or adding recipes altogether.

In a system with immutable infrastructure, on the other hand, all changes involve destroying and fully recreating resources. Containers like Docker, and container orchestration systems like Kubernetes, are good examples of systems with immutable infrastructure.

Which is better? It depends on what you're looking for. In a pure Chef system, creating new resources is costly because you create them from scratch every time, but every subsequent update is relatively cheap. In a pure immutable infrastructure system, the cost is in the creation of the image itself, so it depends on how frequently you make changes to the image, but creating new resources from that image is usually pretty cheap. A lot of places have arrived at a hybrid approach for this reason, where they have base images that are updated occasionally, and then have software that incrementally adds other software and configuration that's updated more frequently on top of what's being installed in the base image.

Like many things in software engineering, the question of whether to use mutable or immutable infrastructure (or a hybrid) has no one right answer. It all depends on what you currently do, how costly it would be to change, and where you and your organization ultimately want to spend your engineering time and energy.