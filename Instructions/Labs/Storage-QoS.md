
# Lab: Storage QoS

## Setup

1. On CL1, sign in as **ad\Administrator**.
1. On VN1-SRV4, sign in as **ad\Administrator**.
1. In SConfig, enter **15**.

## Exercise 1: Move virtual machine data

### Task 1: Move virtual machine data to the SOFS share

Perform this task on the owner node of VN1-SRV24.

1. Sign in as **ad\Administrator**.
1. In SConfig, enter **15**.
1. Move the data of VN1-SRV24 to **\\\\VN1-CLST2-SOFS\\Hyper-V Data\\**

   ````powershell
   Move-VMStorage `
      -Name VN1-SRV24 `
      -DestinationStoragePath '\\vn1-clst2-sofs\Hyper-V Data'
   ````

### Task

Perform this task on the owner node of VN1-SRV24.

1. Sign in as **ad\Administrator**.
1. In SConfig, enter **15**.
1. Move the virtual machine VN1-SRV24 to the host VN1-SRV4. Move all data to **C:\ClusterStorage\Volumex\Hyper-V**, where x is the volume number you recorded for the 80 GB disk on VN1-CLST1 in the previous lab.

    ````powershell
    Move-VM `
        -Name VN1-SRV24 `
        -DestinationHost VN1-SRV4 `
        -DestinationStoragePath 'c:\clusterstorage\volumex\Hyper-V' # Replace x
    ````

### Task

Peform this task on VN-SRV4.

1. Move all virtual machine data of **VN1-SRV24** to **\\\\VN1-CLST2-SOFS2\\Hyper-V Data**.

    ````powershell
    Move-VMStorage `
        -Name VN1-SRV24 `
        -DestinationStoragePath '\\VN1-CLST2-SOFS2\Hyper-V Data'
    ````

### Task

Perform this task on CL1.

1. Open **Failover Cluster Manager**.
1. In Failover Cluster Manager, expand **VN1-CLST1.ad.adatum.com** and click **Roles**.
1. In the context-menu of **Roles**, click **Configure Role...**.
1. In High Availability Wizard, on page Before You Begin, click **Next >**.
1. On page Select Role, click **Virtual Machine** and click **Next >**.
1. On page Select Virtual Machine, activate the checkbox next to **VN1-SRV24** and click **Next >**.
1. On page Confirmation, click **Next >**.
1. On page Summary, click **Finish**.
