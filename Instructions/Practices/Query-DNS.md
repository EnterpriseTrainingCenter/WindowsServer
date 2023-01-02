# Practice: Query DNS

## Required VMs

* VN1-SRV1
* VN1-SRV5
* CL1

## Task

Resolve the name **microsoft.com**. Repeat the command several times.

> Why are you receiving more than one IP address?

> What happens to the order of the IP addresses?

> Why did the TTL values decrease?

Resolve the name **microsoft.com** using the local DNS client cache.

> Can you resolve the name using the local DNS client cache?

Resolve the name **etc.at** using the local DNS client cache.

> Can you resolve the name using the local DNS client cache?

Resolve the name **etc.at** without using the local cache, then resolve the name **etc.at** using the local DNS client cache again.

> Can you resolve the name using the local DNS client cache now?

Resolve the MX record for **microsoft.com**. Query the SRV record for **_sip._tls.microsoft.com**. Resolve the SOA record for **microsoft.com**. Query for the name servers of **microsoft.com**.

Clear the DNS client cache and the DNS server cache of **VN1-SRV1**. Then, query for the name **office.com**. Clear the DNS client cache and the DNS server cache of **VN1-SRV5**. Resolve the name **office.com** using **VN1-SRV5** as DNS server without changing the dns client settings.

> Why did you receive an answer?

> Why did response from VN1-SRV5 take significantly longer than from VN1-SRV1?

Resolve the name **ad.adatum.com** using **VN1-SRV5** as DNS server without changing the DNS client settings.

> Why do you get the error message **DNS name does not exist**?

Display the contents of the DNS client cache.

## Instructions

Perform this task on CL1.

1. Open **Windows Terminal**
1. Resolve the name **microsoft.com**.

    ````powershell
    Resolve-DnsName -Name microsoft.com
    ````

    > You receive more than one IP address, because there are multiple A records for microsoft.com with different IP addresses. This can serve security or load balancing purposes.

    Repeat the command several times.

    > The IP addresses are reordered with each query. This is called DNS round robin and servers load balancing purposes.

    > The TTL values decrease, because the response is served from a cache. The TTL indicates the seconds, the record is allowed to be kept in the cache.

1. Resolve the name **microsoft.com** using the local DNS client cache.

    ````powershell
    Resolve-DnsName -Name microsoft.com -CacheOnly
    ````

    > Most likely, you will receive a response, because the entry is present in the cache.

1. Resolve the name **etc.at** using the local DNS client cache.

    ````powershell
    Resolve-DnsName -Name etc.at -CacheOnly
    ````

    > Most likely, you will receive an error message, because the record is not present in the cache.

1. Resolve the name **etc.at** without using the local cache.

    ````powershell
    Resolve-DnsName -Name etc.at -DnsOnly
    ````

1. Resolve the name **etc.at** using the local DNS client cache again.

    ````powershell
    Resolve-DnsName -Name etc.at -CacheOnly
    ````

    > Most likely, you will receive an error message, because the record still is not present in the cache.

1. Resolve the MX record for **microsoft.com**.

    ````powershell
    Resolve-DnsName -Name microsoft.com -Type MX
    ````

1. Query the SRV record for **_sip._tls.microsoft.com**.

    ````powershell
    Resolve-DnsName -Name _sip._tls.easyon.at -Type SRV
    ````

1. Resolve the SOA record for **microsoft.com**. Make sure, all properties are in the output on the screen.

    ````powershell
    Resolve-DnsName -Name microsoft.com -Type SOA | Format-List *
    ````

1. Query for the name servers of **microsoft.com**.

    ````powershell
    Resolve-DnsName -Name microsoft.com -Type NS
    ````

1. Clear the DNS client cache and the DNS server cache of **VN1-SRV1**. Then, query for the name **office.com**.

    ````powershell
    Clear-DnsClientCache
    Clear-DnsServerCache -ComputerName vn1-srv1.ad.adatum.com -Force
    Resolve-DnsName -Name office.com
    ````

1. Clear the DNS client cache and the DNS server cache of **VN1-SRV5**. Resolve the name **office.com** using **VN1-SRV5** as DNS server without changing the DNS client settings.

    ````powershell
    Clear-DnsClientCache
    Clear-DnsServerCache -ComputerName vn1-srv5.ad.adatum.com -Force
    Resolve-DnsName -Name office.com -Server vn1-srv5.ad.adatum.com
    ````

    > VN1-SRV5 uses the DNS root servers to resolve the query.

    > VN1-SRV1 uses a DNS forwarder with a large cache, which boosts the DNS query performance on small networks.

    If you did not notice the difference, you may repeat the commands of the last two steps, but include the ````Resolve-DnsName```` cmdlet in the ````Measure-Command```` cmdlet, e.g.,

    ````powershell
    Clear-DnsClientCache
    Clear-DnsServerCache -ComputerName vn1-srv1.ad.adatum.com -Force
    Measure-Command { Resolve-DnsName -Name office.com }
    Clear-DnsClientCache
    Clear-DnsServerCache -ComputerName vn1-srv5.ad.adatum.com -Force
    Measure-Command {
        Resolve-DnsName -Name office.com -Server vn1-srv5.ad.adatum.com
    }
    ````

1. Resolve the name **ad.adatum.com**.

    ````powershell
    Resolve-DnsName -Name ad.adatum.com
    ````

    > You should get the IP address 10.1.1.8 as response.

1. Resolve the name **ad.adatum.com** using **VN1-SRV5** as DNS server without changing the DNS client settings.

    ````powershell
    Resolve-DnsName -Name ad.adatum.com -Server vn1-srv5.ad.adatum.com
    ````

    > Why do you get the error message **DNS name does not exist**?

1. Display the contents of the DNS client cache.

    ````powershell
    Get-DnsClientCache
    ````
