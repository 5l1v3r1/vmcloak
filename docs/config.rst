.. toctree::
   :hidden:

   kblayout


Configuration
=============

Since VMCloak 0.3 it no longer uses configuration files for simply creating
VMs. However, a couple of things remain necessary to be performed before
invoking VMCloak.

.. _conf-mounted-iso:

Mounting the ISO Image
^^^^^^^^^^^^^^^^^^^^^^

In order to mount a Windows Installer ISO image a directory should be created
with **root**, and then the image should be mounted on that directory, with
root as well. The following bash snippet depicts how to setup an ISO mount on
a Ubuntu/Debian system.

.. code-block:: bash

    mkdir /mnt/winxp
    mount -o loop,ro winxp.iso /mnt/winxp

In order to mount an image under BSD the following commands might be required.

.. code-block:: bash

    mkdir /mnt/winxp
    vnconfig /dev/vnd0d winxp.iso
    mount -t cd9660 /dev/vnd0d /mnt/winxp

Serial Key
^^^^^^^^^^

``--serial-key`` specifies the **serial key** to be used to install Windows in
the Virtual Machine. Although it is possible to randomize the serial key after
the installation has been finished, a valid serial key is required during
installation, and often times there is no *one serial key to rule them all*,
thus make sure to have a valid serial key at hand.

.. _conf-vmname:

VM Name
^^^^^^^

The Virtual Machine name represents the unique identifier for this VM. This
value is the extra argument on the command line, e.g.,
``cuckoo1`` in ``vmcloak -s conf.ini cuckoo1``.

Semi-required configuration entries
-----------------------------------

A few configuration entries are not required, but should in most cases be
provided.

* :ref:`conf-hostonly-ip`
* :ref:`conf-hostonly-gateway`
* :ref:`conf-hostonly-mask`
* :ref:`conf-hostonly-macaddr`
* :ref:`conf-host-ip`

.. _conf-hostonly-ip:

Guest hostonly IP address
^^^^^^^^^^^^^^^^^^^^^^^^^

The IP address to assign to the Guest is set by ``--hostonly-ip``. It defaults
to ``192.168.56.101``, which is perfectly fine when one only intends to
create one VM. However, if one wants to create multiple VMs, then the static
IP addresses should be unique. Normally one would start counting at
``192.168.56.101`` to ``192.168.56.102``, ``192.168.56.103``, etc.

.. _conf-hostonly-gateway:

Guest hostonly Gateway
^^^^^^^^^^^^^^^^^^^^^^^

The Gateway IP for the Guest to use. Set by ``--hostonly-gateway``

.. _conf-hostonly-mask:

Guest hostonly network mask
^^^^^^^^^^^^^^^^^^^^^^^^^^^

The Network mask for the Guest to use. Set by ``--hostonly-mask``

.. _conf-hostonly-macaddr:

Guest hostonly MAC address
^^^^^^^^^^^^^^^^^^^^^^^^^^

The MAC address for the guest to use. Set by ``--hostonly-macaddr``

.. _conf-host-ip:

Host host-ip
^^^^^^^^^^^^

The IP address of the vboxnet interface for communication between guest and host.
The communication is relevant for the installation process to finish.
Set by ``--host-ip``

Suggested configuration entries
-------------------------------

Following are various configuration entries that are not necessary, but
allow one to do some custom modifications on the guest VM, which can be quite
useful if one needs to make a special VM for a custom analysis.

* :ref:`conf-ramsize`
* :ref:`conf-resolution`
* :ref:`conf-hdsize`
* :ref:`conf-hwvirt`
* :ref:`conf-keyboard-layout`

.. _conf-ramsize:

RAM Size
^^^^^^^^

With ``--ramsize`` one can specify the required RAM size of the VM in
megabytes. By default this value will be set to **1024** (which results to
1 GB of RAM).

.. _conf-resolution:

Resolution
^^^^^^^^^^

``--resolution`` sets the resolution of the VM. By default the resolution
will be set to **1024x768**, a not too uncommon resolution if your PC was
bought in the year 2006.

.. _conf-hdsize:

Harddrive Size
^^^^^^^^^^^^^^

``--hdsize`` allows one to specify the harddrive size of the VM in megabytes.
As the created harddrive is enlarged in size on-demand, rather than allocating
all of the specified space at once, it doesn't really matter whether one puts
32 GB or 256 GB. By default this value is set to 256 GB, but this does mean
that if at some point a VM really needs the 256 GB that the harddrive
shouldn't run out of space.

.. _conf-hwvirt:

Hardware Virtualization
^^^^^^^^^^^^^^^^^^^^^^^

If one hasn't enabled **VT-x** in the BIOS then it is not possible to use
hardware virtualization. If one gets such error, then provide ``--no-hwvirt``.
To explicitly enable hardware virtualization provide ``--hwvirt``. In the config
file disable it with ``hwvirt = false``.

.. _conf-keyboard-layout:

Keyboard Layout
^^^^^^^^^^^^^^^

By default the ``--keyboard-layout`` defaults to **US**. See
:ref:`data-keyboard-layout` for a list of all available keyboard layouts.

Cuckoo Sandbox configuration entries
------------------------------------

These configuration entries are related to direct interaction with Cuckoo
Sandbox as VMCloak has the ability to automatically add the created VM to
Cuckoo Sandbox.

* :ref:`conf-cuckoo-directory`
* :ref:`conf-cuckoo-tags`
* :ref:`conf-no-register-cuckoo`

.. _conf-cuckoo-directory:

Cuckoo Directory
^^^^^^^^^^^^^^^^

In order to add a created VM automatically to Cuckoo Sandbox one must run a
recent version of Cuckoo Sandbox (**1.2-dev** or higher) which ships the
``./utils/machine.py`` utility script. The ``--cuckoo`` argument accepts a
path to the root of your Cuckoo Sandbox setup to interact with Cuckoo Sandbox.

.. _conf-cuckoo-tags:

Tags
^^^^

Optionally ``--tags`` adds tags for the created VM when registering it with
Cuckoo Sandbox. For example, if you install the *dotnet40* (.NET v4.0) and
*adobepdf* (Adobe PDF Reader) dependencies in your VM, then you might want to
represent this in its Cuckoo Sandbox tags as ``dotnet,adobe`` or something
like this. For more information on tags, see
`the official Cuckoo Sandbox documentation
<http://docs.cuckoosandbox.org/en/latest/usage/submit/?highlight=tags>`_.

.. _conf-no-register-cuckoo:

No Register Cuckoo
^^^^^^^^^^^^^^^^^^

If the ``--cuckoo`` argument is not provided, or it is provided but the
created VM should not be registered with Cuckoo Sandbox, then the
``--no-register-cuckoo`` argument allows one to do that.

.. _conf-dns-server:

DNS Server
^^^^^^^^^^

By default the generated Virtual Machine will use Google's 8.8.8.8 DNS Server.
This can be changed through ``--dns-server``.

Debugging configuration entries
-------------------------------

As is there's not much debugging one can do. This limits one to *visual*
debugging as described as per :ref:`conf-vm-visible`.

.. _conf-vm-visible:

Visible VM Generation
^^^^^^^^^^^^^^^^^^^^^

The ``--vm-visible`` argument, if specified, runs the Virtual Machine in
**GUI** mode instead of **headless** mode (terms as per VirtualBox.) This
allows one to monitor the installation as it goes.

Often times the installation will hang at the serial key dialog. This is the
case when the :ref:`conf-serial-key` provided is incorrect. At this point
VMCloak is unable to detect it when this happens.
