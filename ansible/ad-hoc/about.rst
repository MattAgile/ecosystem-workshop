******
Ad Hoc
******


Inventory - Host file
=====================
* More in ``Inventory`` chapter
* Ansible (unless specified) command looks for inventory files in ``/etc/ansible/hosts``

.. code-block:: console

    $ cat ./hosts
    [myserver]
    127.0.0.1

    $ ansible -i ./hosts myserver -u ubuntu -a '/bin/grep PRETTY_NAME /etc/os-release'
    127.0.0.1 | CHANGED | rc=0 >>
    PRETTY_NAME="Ubuntu 22.04 LTS"

    $ ansible -i ./hosts all -u ubuntu -a '/bin/grep PRETTY_NAME /etc/os-release'
    127.0.0.1 | CHANGED | rc=0 >>
    PRETTY_NAME="Ubuntu 22.04 LTS"


Syntax
======
* By default Ansible uses only 5 simultaneous processes.
* ``/usr/bin/ansible`` will default to running from your user account
* ``-f 10`` - Sets 10 parallel forks

.. code-block:: console

    $ ansible [pattern] -m [module] -a '[module options]'
    $ ansible [pattern] -m [module] -a '[module options]' -f 10


Become (Run as root)
====================
.. code-block:: console
    :caption: Run as root

    $ ansible [pattern] -m [module] -a '[module options]' --become

.. code-block:: console
    :caption: Example

    $ ansible myserver -a '/usr/bin/whoami' -u ubuntu
    127.0.0.1 | CHANGED | rc=0 >>
    ubuntu

    $ ansible myserver -a '/usr/bin/whoami' -u ubuntu --become
    127.0.0.1 | CHANGED | rc=0 >>
    root


Examples
========
.. code-block:: console

    $ ansible myserver -u ubuntu -m raw -a '/usr/bin/whoami'
    127.0.0.1 | CHANGED | rc=0 >>
    ubuntu
    Shared connection to 127.0.0.1 closed.

    $ ansible myserver -u ubuntu -m shell -a '/usr/bin/whoami'
    127.0.0.1 | CHANGED | rc=0 >>
    ubuntu

    $ ansible myserver -u ubuntu -m command -a '/usr/bin/whoami'
    127.0.0.1 | CHANGED | rc=0 >>
    ubuntu

    $ ansible myserver -u ubuntu -a '/usr/bin/whoami'
    127.0.0.1 | CHANGED | rc=0 >>
    ubuntu


Facts
=====
.. code-block:: console
    :caption: See all facts

    $ ansible myserver -m setup
    $ ansible all -m setup


Installing Packages
===================

Package
-------
* Detects operating system and executes apt, yum or rpm
* Universal and portable
* Preferred method

.. code-block:: console
    :caption: Package module

    $ ansible myserver -m package -a 'name=python3'
    $ ansible myserver -m package -a 'name=python3 state=present'
    $ ansible myserver -m package -a 'name=python3 state=absent'
    $ ansible myserver -m package -a 'name=python3 state=latest'
    $ ansible myserver -m package -a 'name=python3 update_cache=yes state=latest'

Pip
---
.. code-block:: console
    :caption: Make sure python3 and pip is installed

    $ ansible myserver -m package -a 'name=python3-pip state=present'

.. code-block:: ini
    :caption: Inventory file. Make sure to use python3 interpreter

    [myserver]
    127.0.0.1 ansible_python_interpreter=/usr/bin/python3

.. code-block:: console
    :caption: Pip module

    $ ansible myserver -m pip -a 'name=numpy'
    $ ansible myserver -m pip -a 'name=numpy state=present'
    $ ansible myserver -m pip -a 'name=numpy state=absent'
    $ ansible myserver -m pip -a 'name=numpy state=latest'
    $ ansible myserver -m pip -a 'name=numpy update_cache=yes state=latest'

Apt
---
.. code-block:: console
    :caption: Apt module

    $ ansible myserver -m apt -a 'name=python3'
    $ ansible myserver -m apt -a 'name=python3 state=present'
    $ ansible myserver -m apt -a 'name=python3 state=absent'
    $ ansible myserver -m apt -a 'name=python3 state=latest'
    $ ansible myserver -m apt -a 'name=python3 update_cache=yes state=latest'

Yum
---
.. code-block:: console
    :caption: Yum module

    $ ansible myserver -m yum -a 'name=python3'
    $ ansible myserver -m yum -a 'name=python3 state=present'
    $ ansible myserver -m yum -a 'name=python3 state=absent'
    $ ansible myserver -m yum -a 'name=python3 state=latest'
    $ ansible myserver -m yum -a 'name=python3 update_cache=yes state=latest'

Example
-------
.. code-block:: console
    :caption: apt module

    $ ansible myserver -u ubuntu --become -m apt -a 'name=python3 state=present'
    127.0.0.1 | SUCCESS => {
        "ansible_facts": {
            "discovered_interpreter_python": "/usr/bin/python"
        },
        "cache_update_time": 1578970172,
        "cache_updated": false,
        "changed": false
    }

.. code-block:: console

    $ ansible localhost -m apt -a 'name=nmap state=latest'
    [WARNING]: No inventory was parsed, only implicit localhost is available

    [WARNING]: Updating cache and auto-installing missing dependency: python3-apt

    localhost | CHANGED => {
        "cache_update_time": 1578958622,
        "cache_updated": false,
        "changed": true,
        "stderr": "debconf: delaying package configuration, since apt-utils is not installed\n",
        "stderr_lines": [
            "debconf: delaying package configuration, since apt-utils is not installed"
        ],
        "stdout": "Reading package lists...\nBuilding dependency tree...\nReading state information...\nThe following additional packages will be installed:\n  libblas3 liblinear3 liblua5.3-0 libpcap0.8\nSuggested packages:\n  liblinear-tools liblinear-dev ndiff\nThe following NEW packages will be installed:\n  libblas3 liblinear3 liblua5.3-0 libpcap0.8 nmap\n0 upgraded, 5 newly installed, 0 to remove and 2 not upgraded.\nNeed to get 5585 kB of archives.\nAfter this operation, 25.3 MB of additional disk space will be used.\nGet:1 http://archive.ubuntu.com/ubuntu bionic-updates/main amd64 libpcap0.8 amd64 1.8.1-6ubuntu1.18.04.1 [118 kB]\nGet:2 http://archive.ubuntu.com/ubuntu bionic/main amd64 libblas3 amd64 3.7.1-4ubuntu1 [140 kB]\nGet:3 http://archive.ubuntu.com/ubuntu bionic/main amd64 liblinear3 amd64 2.1.0+dfsg-2 [39.3 kB]\nGet:4 http://archive.ubuntu.com/ubuntu bionic-updates/main amd64 liblua5.3-0 amd64 5.3.3-1ubuntu0.18.04.1 [115 kB]\nGet:5 http://archive.ubuntu.com/ubuntu bionic/main amd64 nmap amd64 7.60-1ubuntu5 [5174 kB]\nFetched 5585 kB in 0s (28.6 MB/s)\nSelecting previously unselected package libpcap0.8:amd64.\r\n(Reading database ... \r(Reading database ... 5%\r(Reading database ... 10%\r(Reading database ... 15%\r(Reading database ... 20%\r(Reading database ... 25%\r(Reading database ... 30%\r(Reading database ... 35%\r(Reading database ... 40%\r(Reading database ... 45%\r(Reading database ... 50%\r(Reading database ... 55%\r(Reading database ... 60%\r(Reading database ... 65%\r(Reading database ... 70%\r(Reading database ... 75%\r(Reading database ... 80%\r(Reading database ... 85%\r(Reading database ... 90%\r(Reading database ... 95%\r(Reading database ... 100%\r(Reading database ... 15076 files and directories currently installed.)\r\nPreparing to unpack .../libpcap0.8_1.8.1-6ubuntu1.18.04.1_amd64.deb ...\r\nUnpacking libpcap0.8:amd64 (1.8.1-6ubuntu1.18.04.1) ...\r\nSelecting previously unselected package libblas3:amd64.\r\nPreparing to unpack .../libblas3_3.7.1-4ubuntu1_amd64.deb ...\r\nUnpacking libblas3:amd64 (3.7.1-4ubuntu1) ...\r\nSelecting previously unselected package liblinear3:amd64.\r\nPreparing to unpack .../liblinear3_2.1.0+dfsg-2_amd64.deb ...\r\nUnpacking liblinear3:amd64 (2.1.0+dfsg-2) ...\r\nSelecting previously unselected package liblua5.3-0:amd64.\r\nPreparing to unpack .../liblua5.3-0_5.3.3-1ubuntu0.18.04.1_amd64.deb ...\r\nUnpacking liblua5.3-0:amd64 (5.3.3-1ubuntu0.18.04.1) ...\r\nSelecting previously unselected package nmap.\r\nPreparing to unpack .../nmap_7.60-1ubuntu5_amd64.deb ...\r\nUnpacking nmap (7.60-1ubuntu5) ...\r\nSetting up libblas3:amd64 (3.7.1-4ubuntu1) ...\r\nupdate-alternatives: using /usr/lib/x86_64-linux-gnu/blas/libblas.so.3 to provide /usr/lib/x86_64-linux-gnu/libblas.so.3 (libblas.so.3-x86_64-linux-gnu) in auto mode\r\nSetting up liblinear3:amd64 (2.1.0+dfsg-2) ...\r\nSetting up liblua5.3-0:amd64 (5.3.3-1ubuntu0.18.04.1) ...\r\nSetting up libpcap0.8:amd64 (1.8.1-6ubuntu1.18.04.1) ...\r\nSetting up nmap (7.60-1ubuntu5) ...\r\nProcessing triggers for libc-bin (2.27-3ubuntu1) ...\r\n",
        "stdout_lines": [
            "Reading package lists...",
            "Building dependency tree...",
            "Reading state information...",
            "The following additional packages will be installed:",
            "  libblas3 liblinear3 liblua5.3-0 libpcap0.8",
            "Suggested packages:",
            "  liblinear-tools liblinear-dev ndiff",
            "The following NEW packages will be installed:",
            "  libblas3 liblinear3 liblua5.3-0 libpcap0.8 nmap",
            "0 upgraded, 5 newly installed, 0 to remove and 2 not upgraded.",
            "Need to get 5585 kB of archives.",
            "After this operation, 25.3 MB of additional disk space will be used.",
            "Get:1 http://archive.ubuntu.com/ubuntu bionic-updates/main amd64 libpcap0.8 amd64 1.8.1-6ubuntu1.18.04.1 [118 kB]",
            "Get:2 http://archive.ubuntu.com/ubuntu bionic/main amd64 libblas3 amd64 3.7.1-4ubuntu1 [140 kB]",
            "Get:3 http://archive.ubuntu.com/ubuntu bionic/main amd64 liblinear3 amd64 2.1.0+dfsg-2 [39.3 kB]",
            "Get:4 http://archive.ubuntu.com/ubuntu bionic-updates/main amd64 liblua5.3-0 amd64 5.3.3-1ubuntu0.18.04.1 [115 kB]",
            "Get:5 http://archive.ubuntu.com/ubuntu bionic/main amd64 nmap amd64 7.60-1ubuntu5 [5174 kB]",
            "Fetched 5585 kB in 0s (28.6 MB/s)",
            "Selecting previously unselected package libpcap0.8:amd64.",
            "(Reading database ... ",
            "(Reading database ... 5%",
            "(Reading database ... 10%",
            "(Reading database ... 15%",
            "(Reading database ... 20%",
            "(Reading database ... 25%",
            "(Reading database ... 30%",
            "(Reading database ... 35%",
            "(Reading database ... 40%",
            "(Reading database ... 45%",
            "(Reading database ... 50%",
            "(Reading database ... 55%",
            "(Reading database ... 60%",
            "(Reading database ... 65%",
            "(Reading database ... 70%",
            "(Reading database ... 75%",
            "(Reading database ... 80%",
            "(Reading database ... 85%",
            "(Reading database ... 90%",
            "(Reading database ... 95%",
            "(Reading database ... 100%",
            "(Reading database ... 15076 files and directories currently installed.)",
            "Preparing to unpack .../libpcap0.8_1.8.1-6ubuntu1.18.04.1_amd64.deb ...",
            "Unpacking libpcap0.8:amd64 (1.8.1-6ubuntu1.18.04.1) ...",
            "Selecting previously unselected package libblas3:amd64.",
            "Preparing to unpack .../libblas3_3.7.1-4ubuntu1_amd64.deb ...",
            "Unpacking libblas3:amd64 (3.7.1-4ubuntu1) ...",
            "Selecting previously unselected package liblinear3:amd64.",
            "Preparing to unpack .../liblinear3_2.1.0+dfsg-2_amd64.deb ...",
            "Unpacking liblinear3:amd64 (2.1.0+dfsg-2) ...",
            "Selecting previously unselected package liblua5.3-0:amd64.",
            "Preparing to unpack .../liblua5.3-0_5.3.3-1ubuntu0.18.04.1_amd64.deb ...",
            "Unpacking liblua5.3-0:amd64 (5.3.3-1ubuntu0.18.04.1) ...",
            "Selecting previously unselected package nmap.",
            "Preparing to unpack .../nmap_7.60-1ubuntu5_amd64.deb ...",
            "Unpacking nmap (7.60-1ubuntu5) ...",
            "Setting up libblas3:amd64 (3.7.1-4ubuntu1) ...",
            "update-alternatives: using /usr/lib/x86_64-linux-gnu/blas/libblas.so.3 to provide /usr/lib/x86_64-linux-gnu/libblas.so.3 (libblas.so.3-x86_64-linux-gnu) in auto mode",
            "Setting up liblinear3:amd64 (2.1.0+dfsg-2) ...",
            "Setting up liblua5.3-0:amd64 (5.3.3-1ubuntu0.18.04.1) ...",
            "Setting up libpcap0.8:amd64 (1.8.1-6ubuntu1.18.04.1) ...",
            "Setting up nmap (7.60-1ubuntu5) ...",
            "Processing triggers for libc-bin (2.27-3ubuntu1) ..."
        ]
    }
