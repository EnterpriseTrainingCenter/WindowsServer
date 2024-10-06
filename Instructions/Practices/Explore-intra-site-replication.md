# Practice: Explore intra-site replication

## Required VMs

* VN1-SRV5
* CL1

If you did not complete the lab [Deploying domain controllers](Deploying-domain-controllers.md), in addition to the VMs above, **VN1-SRV1** is required. If VN1-SRV1 is already shut down after the lab, do not start it.

## Setup

If you skipped the practice [Install Remote Server Administration Tools](Practices/Install-Remote-Server-Administration-Tools.md), on **CL1**, in **Terminal**, execute ````C:\LabResources\Solutions\Install-RemoteServerAdministrationTools.ps1````.

If you did not deploy additional domain controllers and domains in the previous labs, the results of this practice may be limited.

## Task

Explore the automatically created connection objects and write a documentation of the replication topology for each naming context.

## Instructions

Perform these steps on CL1.

1. Sign in as **Administrator@ad.adatum.com**.
1. Open **Active Directory Sites and Services**.
1. Expand **Default-First-Site-Name** and **Servers**.
1. For each server, do the following:

    1. Expand the server and click **NTDS Settings**.
    1. Double-click each connection object.
    1. In \<automatically generated\> Properties, take a note of the following properties to fill a table like the example shown below:

        * Under **Replicate from** the property **Server**
        * **Replicated Naming Context(s)** (move the text cursor to the end of the text field, to see all naming contexts)
        * **Partially Replicated Naming Context(s)**

        | Naming context               | Partial | From server | To server |
        | ---------------------------- | ------- | ----------- | --------- |
        | ForestDnsZones.ad.adatum.com |         | PM-SRV1     | VN1-SRV5  |
        |                              |         | VN2-SRV1    | VN2-SRV5  |
        |                              |         | PM-SRV1     | VN1-SRV7  |
        |                              |         | VN2-SRV1    | VN1-SRV7  |
        |                              |         | VN1-SRV7    | VN2-SRV1  |
        |                              |         | VN1-SRV5    | VN2-SRV1  |
        |                              |         | VN1-SRV5    | PM-SRV1   |
        |                              |         | VN1-SRV7    | PM-SRV1   |
        | DomainDnsZones.ad.adatum.com |         | VN2-SRV1    | VN1-SRV5  |
        |                              |         | VN1-SRV5    | VN2-SRV1  |
        | ad.adatum.com                |         | VN2-SRV1    | VN2-SRV5  |
        |                              |         | VN1-SRV5    | VN2-SRV1  |
        | Schema                       |         | PM-SRV1     | VN1-SRV5  |
        |                              |         | VN2-SRV1    | VN2-SRV5  |
        |                              |         | PM-SRV1     | VN1-SRV7  |
        |                              |         | VN2-SRV1    | VN1-SRV7  |
        |                              |         | VN1-SRV7    | VN2-SRV1  |
        |                              |         | VN1-SRV5    | VN2-SRV1  |
        |                              |         | VN1-SRV5    | PM-SRV1   |
        |                              |         | VN1-SRV7    | PM-SRV1   |
        | Configuration                |         | PM-SRV1     | VN1-SRV5  |
        |                              |         | VN2-SRV1    | VN2-SRV5  |
        |                              |         | PM-SRV1     | VN1-SRV7  |
        |                              |         | VN2-SRV1    | VN1-SRV7  |
        |                              |         | VN1-SRV7    | VN2-SRV1  |
        |                              |         | VN1-SRV5    | VN2-SRV1  |
        |                              |         | VN1-SRV5    | PM-SRV1   |
        |                              |         | VN1-SRV7    | PM-SRV1   |
        | All other domains            | Yes     | PM-SRV1     | VN1-SRV5  |
        |                              |         | VN2-SRV1    | VN2-SRV5  |
        |                              |         | PM-SRV1     | VN1-SRV7  |
        |                              |         | VN2-SRV1    | VN1-SRV7  |
        |                              |         | VN1-SRV7    | VN2-SRV1  |
        |                              |         | VN1-SRV5    | VN2-SRV1  |
        |                              |         | VN1-SRV5    | PM-SRV1   |
        |                              |         | VN1-SRV7    | PM-SRV1   |

        > Why are there so my connection objects for ForestDnsZones, Schema, Configuration, and all other domains, while there are only two for DomainDnsZones and ad.adatum.com?

    1. If time permits, draw a diagram of the replication topology for each naming context.
