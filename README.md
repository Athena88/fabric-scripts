# GOV.UK Fabric Scripts

[Fabric](http://fabfile.org) is a command-line tool for application deployment
and systems administration tasks. It allows the user to run commands across a
server farm.

## Local usage

This is intended to be setup and run on your local workstation/laptop.

### Setup

To install the dependencies:

    $ pip install -Ur requirements.txt

NB: if you get a "pip: command not found" error, run this first:

    $ sudo easy_install pip

Configure it (see [the fabric documentation][fabdoc] for more examples),

    $ echo 'user = jimbob' >> ~/.fabricrc

[fabdoc]: http://docs.fabfile.org/en/latest/usage/fab.html

### Commands

You can view a list of the available tasks:

    $ fab -l

And execute against an environment and set of hosts like so:

    $ fab integration all hosts
    ...
    $ fab integration class:frontend do:'uname -a'
    $ fab integration class:cache,bouncer do:uptime
    ...

### Targetting groups of machines

Fabric tasks can be run on groups of machines in a variety of different ways.

by puppet class

    # target all machines that have the 'govuk::safe_to_reboot::yes' class
    $ fab integration puppet_class:govuk::safe_to_reboot::yes do:'uname -a'

by numeric machine suffix

    # target all machines that end in '2'
    $ fab integration numbered:2 do:'uname -a'

by node type (as defined in puppet)

    # target all 'frontend' machines
    $ fab integration node_type:frontend do:'uname -a'

by the node name

    # target just one node
    $ fab production -H backend-3.backend do:'uname -a'

## Remote usage (legacy)

It was previously possible to use these scripts from the jumpboxes via the
`govuk_fab` wrapper. This is no longer supported as it relied on SSH agent
forwarding, which is a Bad Thing because other people with root access to
that jumpbox would be able to re-use any of the private keys in your agent.

You should convert to the [local usage](#Local usage) method described above
and ensure that you do not have the following option anywhere in your
`~/.ssh/config`:

    ForwardAgent yes

## Syncing postgres machines

An example

`fab <env> -H '<src_db>' postgresql.sync:<db_name>,<dst_db> -A`

the -A must be specified to forward the agent

This will sync the specified database `<db_name>` from the machine with the
hostname of `<src_db>` to the machine with hostaname `<dst_db>`. It will destroy
data on the destination db
