# Practice: Add a DHCP scope

## Required VMs

* VN1-SRV1
* VN1-SRV6
* CL1

## Task

On VN1-SRV6, add a scope with the range 10.1.1.2 to 10.1.1.254 with a lease duration of 2 hours. Configure the router option to 10.1.1.1.

> Why would you prefer a short lease duration?

> Why do you not configure DNS options?

## Instructions

Perform this task on CL1.

1. Open **DHCP**.
1. In DHCP, in the context-menu of **DHCP**, click **Add Server...**
1. In Add Server, under **This server**, type **VN1-SRV6** and click **OK**.
1. In **DHCP**, expand  **vn1-srv6.ad.adatum.com**, and click **IPv4**.
1. In the context-menu of **IPv4**, click **New Scope...**
1. In the New Scope Wizard, on page Welcome to the New Scope Wizard, click **Next >**.
1. On page Scope Name, in **Name**, type **VNet1** and click **Next >**.
1. On page IP Address Range, in **Start IP address**, type **10.1.1.2**. In **End IP address**, type **10.1.1.254**. In **Length**, type **24**. Click **Next >**

    In **Length**, you can also click the up arrow button to increase it to 24. Alternatively, in **Subnet mask**, you could type **255.255.255.0**.

1. On page Add Exclusions and Delay, click **Next >**.
1. On page Lease Duration, in **Days**, type **0**, in **Hours**, type **2**, and click **Next >**.

    > A short lease duration allow for quicker reconfiguration and uses the IP address range more efficiently. To mitigate server failures, we will configure fault-tolerance later.

1. On page Configure DHCP options, ensure **Yes, I want to configure these options now** is selected, and click **Next >**.
1. On page Router (Default Gateway), under **IP address**, type **10.1.1.1**, click **Add** and click **Next >**.
1. On page Domain Name and DNS Servers, In **Parent domain**, delete the text (leave it blank). Under **IP address**, click **10.1.1.8** and click **Remove**. Click **Next >**.

    > These options are configured server-wide already.

1. On page WINS Servers, click **Next >**.
1. On page Activate Scope, click **No, I will activate this scope later** and click **Next >**.
1. On Completing the New Scope Wizard, click **Finish**.
