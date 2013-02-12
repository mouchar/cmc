SSH is a powerful tool. When configured correctly it should not only provide
excellent security, it should also make your work *easier* and
*more convenient*.

Assumptions
===========
1. You're using Mac OS X

   - This is not a requirement. It only impacts the portions that reference
     the Mac Keychain.

2. You have already created a SSH key pair and added the secret key to your
   Mac keychain.

3. Your name is Arthur Dent

   - Your username on your Mac laptop is ``arthurdent``
   - Your username on remote systems is ``arthur``

4. Only the ``~/.ssh/config`` on your laptop will ever need to be edited.

5. Three hypothetical hosts (see below)


Example ``~/.ssh/config`` Configuration
---------------------------------------

::

    # insecure
    Host insecure insecure.example.com
        HostName insecure.example.com

    # bastion
    Host bastion bastion.example.com
        Hostname bastion.example.com
        ForwardAgent yes
        ControlMaster auto
        ControlPath ~/.ssh/master-%r@%h:%p

    # production
    Host prod production prod*.example.com
        HostName production.example.com
        ForwardAgent yes
        ProxyCommand ssh -q bastion nc -w30 %h %p

    # global defaults
    Host *
        User arthur
        ServerAliveCountMax 6
        ServerAliveInterval 10


Please also see the `annotated ssh configuration example`_ with explanations.

.. _`annotated ssh configuration example`: annotated_config.rst


Workflow
========

1. Establish control sessions at the start of your day/session/etc.

   - ``mcc bastion``
   - This establishes a control master connection in the background. It will
     stay connected and available until it is closed or connectivity is lost.

2. Connect "directly" to hosts behind the firewall using bastion as a proxy

   - ``ssh prod``
   - Since ``mcc`` was already used to put a session to bastion in the
     background, it is reused.


References
==========

- `ssh_config(5) OS X Manual Page`_

.. _`ssh_config(5) OS X Manual Page`:
   https://developer.apple.com/library/mac/#documentation/Darwin/Reference/ManPages/man5/ssh_config.5.html