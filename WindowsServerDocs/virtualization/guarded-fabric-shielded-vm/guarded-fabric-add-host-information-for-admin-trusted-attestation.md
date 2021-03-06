---
title: Add host information for Admin-trusted attestation
ms.custom: na
ms.prod: windows-server-threshold
ms.topic: article
ms.assetid: 87089ebc-b953-4aa3-96b5-966cf91acb02
manager: dongill
author: rpsqrd
ms.technology: security-guarded-fabric
ms.date: 03/02/2017
---

>Applies To: Windows Server 2016

# Add host information for Admin-trusted attestation

The following two sections describe how to configure DNS forwarding, and add host information to HGS for AD mode:

- [Configuring DNS forwarding and domain trust](#configure-dns-forwarding-and-domain-trust)
- [Adding security group information to the HGS configuration](#add-security-group-information-to-the-hgs-configuration)

## Configure DNS forwarding and domain trust

Use the following steps to set up necessary DNS forwarding from the HGS domain to the fabric domain, and to establish a one-way forest trust to the fabric domain. These steps allow the HGS to locate the fabric domain's domain controllers and validate group membership of the Hyper-V hosts.

1.  To configure a DNS forwarder that allows HGS to resolve resources located in the fabric (host) domain, run the following command in an elevated PowerShell session. 

    Replace fabrikam.com with the name of the fabric domain and type the IP addresses of DNS servers in the fabric domain. For higher availability, point to more than one DNS server.

    ```powershell
    Add-DnsServerConditionalForwarderZone -Name "fabrikam.com" -ReplicationScope "Forest" -MasterServers <DNSserverAddress1>, <DNSserverAddress2>
    ```

2.  To create a one-way forest trust from the HGS domain to the fabric domain, run the following command in an elevated Command Prompt:

    Replace `relecloud.com` with the name of the HGS domain and `fabrikam.com` with the name of the fabric domain. Provide the password for and admin of the fabric domain.

        netdom trust relecloud.com /domain:fabrikam.com /userD:fabrikam.com\Administrator /passwordD:<password> /add

## Add security group information to the HGS configuration 

1.  Obtain the SID of the [security group](guarded-fabric-admin-trusted-attestation-creating-a-security-group.md#create-a-security-group-and-add-hosts) for guarded hosts from the fabric administrator and run the following command to register the security group with HGS. Re-run the command if necessary for additional groups. Provide a friendly name for the group. It does not need to match the Active Directory security group name. 

    ```powershell
    Add-HgsAttestationHostGroup -Name "<GuardedHostGroup>" -Identifier "<SID>"
    ```

2.  To verify the group was added, run [Get-HgsAttestationHostGroup](https://technet.microsoft.com/library/mt652172.aspx). 

This completes the process of configuring an HGS cluster for AD mode. The fabric administrator might need you to provide two URLs from HGS before the configuration can be completed for the hosts. To obtain these URLs, on an HGS server, run [Get-HgsServer](https://technet.microsoft.com/library/mt652162.aspx).

