
=====================
Generic system models
=====================

This repo contains general reclass system level of salt model. It is to be
used along with service models and concrete cluster deployment model.

Network configuration
=====================

Enable SR-IOV support
---------------------

Include class at `cluster.<name>.openstack.compute`

.. code-block:: yaml

  - system.nova.compute.nfv.sriov

For single SR-IOV interface setup you can set parameters:
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

`sriov_nic01_device_name`
  Name of the Physical Function interface (pF)

`sriov_nic01_numvfs`
  Number of Virtual Functions (VF), for number of 
  supported VF check documentation for your network interface card.

`sriov_nic01_physical_network`
  Default **physnet1**, label for physical network the interface belongs to.

`sriov_unsafe_interrupts`
  Default **False**, needs to be set **True** if your hw platform does not 
  support interrupt remapping.


Multiple SR-IOV interface setup:
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

By default, the metadata model contains configuration for 1 NIC
dedicated for SR-IOV, so we need to setup network interfaces like in the
following example.

.. code-block:: yaml

      ...
        nova:
          compute:
            sriov:
              sriov_nic01:
                devname: eth1
                physical_network: physnet3
              sriov_nic02:
                devname: eth2
                physical_network: physnet4
              sriov_nic03:
                devname: eth3
                physical_network: physnet3
              sriov_nic04:
                devname: eth4
                physical_network: physnet6
        linux:
          system:
            kernel:
              sriov: True
              unsafe_interrupts: False
            rc:
              local: |
                #!/bin/sh -e
                # Enabling 7 VFs on eth1 PF
                echo 7 > /sys/class/net/eth1/device/sriov_numvfs; sleep 2; ip link set eth1 up
                # Enabling 15 VFs on eth2 PF
                echo 15 > /sys/class/net/eth2/device/sriov_numvfs; sleep 2; ip link set eth2 up
                # Enabling 15 VFs on eth3 PF
                echo 15 > /sys/class/net/eth3/device/sriov_numvfs; sleep 2; ip link set eth3 up
                # Enabling 7 VFs on eth4 PF
                echo 7 > /sys/class/net/eth4/device/sriov_numvfs; sleep 2; ip link set eth4 up
                exit 0
