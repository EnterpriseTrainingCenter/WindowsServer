# Practice: Explore intra-site replication

## Required VMs

* VN1-SRV5
* CL1

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
        | ForestDnsZones.ad.adatum.com |         | VN3-SRV1    | VN1-SRV5  |
        |                              |         | VN2-SRV1    | VN2-SRV5  |
        |                              |         | VN3-SRV1    | VN1-SRV7  |
        |                              |         | VN2-SRV1    | VN1-SRV7  |
        |                              |         | VN1-SRV7    | VN2-SRV1  |
        |                              |         | VN1-SRV5    | VN2-SRV1  |
        |                              |         | VN1-SRV5    | VN3-SRV1  |
        |                              |         | VN1-SRV7    | VN3-SRV1  |
        | DomainDnsZones.ad.adatum.com |         | VN2-SRV1    | VN1-SRV5  |
        |                              |         | VN1-SRV5    | VN2-SRV1  |
        | ad.adatum.com                |         | VN2-SRV1    | VN2-SRV5  |
        |                              |         | VN1-SRV5    | VN2-SRV1  |
        | Schema                       |         | VN3-SRV1    | VN1-SRV5  |
        |                              |         | VN2-SRV1    | VN2-SRV5  |
        |                              |         | VN3-SRV1    | VN1-SRV7  |
        |                              |         | VN2-SRV1    | VN1-SRV7  |
        |                              |         | VN1-SRV7    | VN2-SRV1  |
        |                              |         | VN1-SRV5    | VN2-SRV1  |
        |                              |         | VN1-SRV5    | VN3-SRV1  |
        |                              |         | VN1-SRV7    | VN3-SRV1  |
        | Configuration                |         | VN3-SRV1    | VN1-SRV5  |
        |                              |         | VN2-SRV1    | VN2-SRV5  |
        |                              |         | VN3-SRV1    | VN1-SRV7  |
        |                              |         | VN2-SRV1    | VN1-SRV7  |
        |                              |         | VN1-SRV7    | VN2-SRV1  |
        |                              |         | VN1-SRV5    | VN2-SRV1  |
        |                              |         | VN1-SRV5    | VN3-SRV1  |
        |                              |         | VN1-SRV7    | VN3-SRV1  |
        | All other domains            | Yes     | VN3-SRV1    | VN1-SRV5  |
        |                              |         | VN2-SRV1    | VN2-SRV5  |
        |                              |         | VN3-SRV1    | VN1-SRV7  |
        |                              |         | VN2-SRV1    | VN1-SRV7  |
        |                              |         | VN1-SRV7    | VN2-SRV1  |
        |                              |         | VN1-SRV5    | VN2-SRV1  |
        |                              |         | VN1-SRV5    | VN3-SRV1  |
        |                              |         | VN1-SRV7    | VN3-SRV1  |

        > Why are there so my connection objects for ForestDnsZones, Schema, Configuration, and all other domains, while there are only two for DomainDnsZones and ad.adatum.com?

    1. If time permits, draw a diagram of the replication topology for each naming context.
