Firewall Rule Format
====================

The JSON documents for firewall rules differ greatly in format between
Azure and Google Cloud Platform (GCP). This document contains examples
of various types of firewall rules in both cloud platforms along with
some analysis of how they may or may not be used in the context of this
project. The final objective of this analysis is to create a common
record format for firewall rules from both cloud platforms.

Note that this document is work in progress. Different types of firewall
rules will be captured in this document gradually in future.


Contents
--------

* [Notation](#notation)
* [Group vs. Rule](#group-vs-rule)
  * [Group vs. Rule: Azure](#group-vs-rule-azure)
  * [Group vs. Rule: GCP](#group-vs-rule-gcp)
  * [Group vs. Rule: Conclusion](#group-vs-rule-conclusion)
* [Multiple Ranges](#multiple-ranges)
  * [Multiple Ranges: Azure](#multiple-ranges-azure)
  * [Multiple Ranges: GCP](#multiple-ranges-gcp)
  * [Multiple Ranges: Conclusion](#multiple-ranges-conclusion)
* [Any Source](#any-source)
  * [Any Source: Azure](#any-source-azure)
    * [`source_address_prefix` set to `*`](#source_address_prefix-set-to-)
    * [`source_address_prefix` set to CIDR](#source_address_prefix-set-to-cidr)
    * [`source_address_prefixes` set to multiple CIDRs](#source_address_prefixes-set-to-multiple-cidrs)
    * [`source_address_prefix` set to `Internet`](#source_address_prefix-set-to-internet)
  * [Any Source: GCP](#any-source-gcp)
  * [Any Source: Conclusion](#any-source-conclusion)
* [Any Port](#any-port)
  * [Any Port: Azure](#any-port-azure)
    * [`destination_port_range` set to `*`](#destination_port_range-set-to-)
    * [`destination_port_range` set to integer range](#destination_port_range-set-to-integer-range)
    * [`destination_port_ranges` set to multiple integer ranges](#destination_port_ranges-set-to-multiple-integer-ranges)
  * [Any Port: GCP](#any-port-gcp)
    * [`ports` missing](#ports-missing)
    * [`ports` set to port range](#ports-set-to-port-range)
  * [Any Port: Conclusion](#any-port-conclusion)


Notation
--------

There are two kinds of notations frequently used in this document. Here
are two examples of such notation:

  - *Source*, i.e., italicized "Source". This is a label picked verbatim
    from the Azure portal.
  - `Any`, i.e., "Any" written in a monospace font. This is either user
    input entered or selected in the Azure portal or snippets of JSON
    properties.


Group vs. Rule
--------------

### Group vs. Rule: Azure

Azure returns a JSON document for an entire network security group
containing a list of one or more firewall rules.

    {
      "id": "/subscriptions/a8daa361-b951-46b5-8e9d-b375f2ffe9fd/resourceGroups/rg1/providers/Microsoft.Network/networkSecurityGroups/vm1-nsg",
      "name": "vm1-nsg",
      "type": "Microsoft.Network/networkSecurityGroups",
      "location": "westindia",
      "tags": {
        "owner": "Humpty Dumpy",
        "location": "Great Wall"
      },
      "security_rules": [
        {
          "id": "/subscriptions/a8daa361-b951-46b5-8e9d-b375f2ffe9fd/resourceGroups/rg1/providers/Microsoft.Network/networkSecurityGroups/vm1-nsg/securityRules/SSH",
          "protocol": "TCP",
          "source_port_range": "*",
          "destination_port_range": "22",
          "source_address_prefix": "*",
          "source_address_prefixes": [],
          "destination_address_prefix": "*",
          "destination_address_prefixes": [],
          "source_port_ranges": [],
          "destination_port_ranges": [],
          "access": "Allow",
          "priority": 300,
          "direction": "Inbound",
          "provisioning_state": "Succeeded",
          "name": "SSH",
          "etag": "W/\"de62381d-d3f8-4b15-924c-bcad4ce8480f\""
        },
        {
          "id": "/subscriptions/a8daa361-b951-46b5-8e9d-b375f2ffe9fd/resourceGroups/rg1/providers/Microsoft.Network/networkSecurityGroups/vm1-nsg/securityRules/HTTPS",
          "protocol": "TCP",
          "source_port_range": "*",
          "destination_port_range": "443",
          "source_address_prefix": "*",
          "source_address_prefixes": [],
          "destination_address_prefix": "*",
          "destination_address_prefixes": [],
          "source_port_ranges": [],
          "destination_port_ranges": [],
          "access": "Allow",
          "priority": 320,
          "direction": "Inbound",
          "provisioning_state": "Succeeded",
          "name": "HTTPS",
          "etag": "W/\"de62381d-d3f8-4b15-924c-bcad4ce8480f\""
        },
        {
          "id": "/subscriptions/a8daa361-b951-46b5-8e9d-b375f2ffe9fd/resourceGroups/rg1/providers/Microsoft.Network/networkSecurityGroups/vm1-nsg/securityRules/HTTP",
          "protocol": "TCP",
          "source_port_range": "*",
          "destination_port_range": "80",
          "source_address_prefix": "*",
          "source_address_prefixes": [],
          "destination_address_prefix": "*",
          "destination_address_prefixes": [],
          "source_port_ranges": [],
          "destination_port_ranges": [],
          "access": "Allow",
          "priority": 340,
          "direction": "Inbound",
          "provisioning_state": "Succeeded",
          "name": "HTTP",
          "etag": "W/\"de62381d-d3f8-4b15-924c-bcad4ce8480f\""
        },
        {
          "id": "/subscriptions/a8daa361-b951-46b5-8e9d-b375f2ffe9fd/resourceGroups/rg1/providers/Microsoft.Network/networkSecurityGroups/vm1-nsg/securityRules/Port_8000",
          "protocol": "*",
          "source_port_range": "*",
          "destination_port_range": "8000",
          "source_address_prefix": "*",
          "source_address_prefixes": [],
          "destination_address_prefix": "*",
          "destination_address_prefixes": [],
          "source_port_ranges": [],
          "destination_port_ranges": [],
          "access": "Allow",
          "priority": 360,
          "direction": "Inbound",
          "provisioning_state": "Succeeded",
          "name": "Port_8000",
          "etag": "W/\"de62381d-d3f8-4b15-924c-bcad4ce8480f\""
        }
      ],
      "default_security_rules": [
        {
          "id": "/subscriptions/a8daa361-b951-46b5-8e9d-b375f2ffe9fd/resourceGroups/rg1/providers/Microsoft.Network/networkSecurityGroups/vm1-nsg/defaultSecurityRules/AllowVnetInBound",
          "description": "Allow inbound traffic from all VMs in VNET",
          "protocol": "*",
          "source_port_range": "*",
          "destination_port_range": "*",
          "source_address_prefix": "VirtualNetwork",
          "source_address_prefixes": [],
          "destination_address_prefix": "VirtualNetwork",
          "destination_address_prefixes": [],
          "source_port_ranges": [],
          "destination_port_ranges": [],
          "access": "Allow",
          "priority": 65000,
          "direction": "Inbound",
          "provisioning_state": "Succeeded",
          "name": "AllowVnetInBound",
          "etag": "W/\"de62381d-d3f8-4b15-924c-bcad4ce8480f\""
        },
        {
          "id": "/subscriptions/a8daa361-b951-46b5-8e9d-b375f2ffe9fd/resourceGroups/rg1/providers/Microsoft.Network/networkSecurityGroups/vm1-nsg/defaultSecurityRules/AllowAzureLoadBalancerInBound",
          "description": "Allow inbound traffic from azure load balancer",
          "protocol": "*",
          "source_port_range": "*",
          "destination_port_range": "*",
          "source_address_prefix": "AzureLoadBalancer",
          "source_address_prefixes": [],
          "destination_address_prefix": "*",
          "destination_address_prefixes": [],
          "source_port_ranges": [],
          "destination_port_ranges": [],
          "access": "Allow",
          "priority": 65001,
          "direction": "Inbound",
          "provisioning_state": "Succeeded",
          "name": "AllowAzureLoadBalancerInBound",
          "etag": "W/\"de62381d-d3f8-4b15-924c-bcad4ce8480f\""
        },
        {
          "id": "/subscriptions/a8daa361-b951-46b5-8e9d-b375f2ffe9fd/resourceGroups/rg1/providers/Microsoft.Network/networkSecurityGroups/vm1-nsg/defaultSecurityRules/DenyAllInBound",
          "description": "Deny all inbound traffic",
          "protocol": "*",
          "source_port_range": "*",
          "destination_port_range": "*",
          "source_address_prefix": "*",
          "source_address_prefixes": [],
          "destination_address_prefix": "*",
          "destination_address_prefixes": [],
          "source_port_ranges": [],
          "destination_port_ranges": [],
          "access": "Deny",
          "priority": 65500,
          "direction": "Inbound",
          "provisioning_state": "Succeeded",
          "name": "DenyAllInBound",
          "etag": "W/\"de62381d-d3f8-4b15-924c-bcad4ce8480f\""
        },
        {
          "id": "/subscriptions/a8daa361-b951-46b5-8e9d-b375f2ffe9fd/resourceGroups/rg1/providers/Microsoft.Network/networkSecurityGroups/vm1-nsg/defaultSecurityRules/AllowVnetOutBound",
          "description": "Allow outbound traffic from all VMs to all VMs in VNET",
          "protocol": "*",
          "source_port_range": "*",
          "destination_port_range": "*",
          "source_address_prefix": "VirtualNetwork",
          "source_address_prefixes": [],
          "destination_address_prefix": "VirtualNetwork",
          "destination_address_prefixes": [],
          "source_port_ranges": [],
          "destination_port_ranges": [],
          "access": "Allow",
          "priority": 65000,
          "direction": "Outbound",
          "provisioning_state": "Succeeded",
          "name": "AllowVnetOutBound",
          "etag": "W/\"de62381d-d3f8-4b15-924c-bcad4ce8480f\""
        },
        {
          "id": "/subscriptions/a8daa361-b951-46b5-8e9d-b375f2ffe9fd/resourceGroups/rg1/providers/Microsoft.Network/networkSecurityGroups/vm1-nsg/defaultSecurityRules/AllowInternetOutBound",
          "description": "Allow outbound traffic from all VMs to Internet",
          "protocol": "*",
          "source_port_range": "*",
          "destination_port_range": "*",
          "source_address_prefix": "*",
          "source_address_prefixes": [],
          "destination_address_prefix": "Internet",
          "destination_address_prefixes": [],
          "source_port_ranges": [],
          "destination_port_ranges": [],
          "access": "Allow",
          "priority": 65001,
          "direction": "Outbound",
          "provisioning_state": "Succeeded",
          "name": "AllowInternetOutBound",
          "etag": "W/\"de62381d-d3f8-4b15-924c-bcad4ce8480f\""
        },
        {
          "id": "/subscriptions/a8daa361-b951-46b5-8e9d-b375f2ffe9fd/resourceGroups/rg1/providers/Microsoft.Network/networkSecurityGroups/vm1-nsg/defaultSecurityRules/DenyAllOutBound",
          "description": "Deny all outbound traffic",
          "protocol": "*",
          "source_port_range": "*",
          "destination_port_range": "*",
          "source_address_prefix": "*",
          "source_address_prefixes": [],
          "destination_address_prefix": "*",
          "destination_address_prefixes": [],
          "source_port_ranges": [],
          "destination_port_ranges": [],
          "access": "Deny",
          "priority": 65500,
          "direction": "Outbound",
          "provisioning_state": "Succeeded",
          "name": "DenyAllOutBound",
          "etag": "W/\"de62381d-d3f8-4b15-924c-bcad4ce8480f\""
        }
      ],
      "network_interfaces": [
        {
          "id": "/subscriptions/a8daa361-b951-46b5-8e9d-b375f2ffe9fd/resourceGroups/rg1/providers/Microsoft.Network/networkInterfaces/vm174"
        }
      ],
      "resource_guid": "9a1b4d8f-067a-4e4b-9456-3d99636e0895",
      "provisioning_state": "Succeeded",
      "etag": "W/\"de62381d-d3f8-4b15-924c-bcad4ce8480f\""
    }

It appears that the rules under the `default_security_rules` bucket are
the default security rules populated by Azure cloud platform itself and
thus likely to be secure. Therefore we would ignore these rules during
any checks by the event-framework unless we find a good reason to not
ignore them in future.

For now, we would analyze the rules under `security_rules` (user-defined
rules).


### Group vs. Rule: GCP

GCP returns each firewall rule as separate top-level JSON objects:

    [
      {
        "kind": "compute#firewall",
        "id": "6741501895436898394",
        "creationTimestamp": "2019-03-17T04:25:41.487-07:00",
        "name": "allow-8000",
        "description": "",
        "network": "https://www.googleapis.com/compute/v1/projects/strong-augury-224506/global/networks/default",
        "priority": 1000,
        "sourceRanges": [
          "0.0.0.0/0"
        ],
        "allowed": [
          {
            "IPProtocol": "tcp",
            "ports": [
              "8000"
            ]
          }
        ],
        "direction": "INGRESS",
        "logConfig": {
          "enable": false
        },
        "disabled": false,
        "selfLink": "https://www.googleapis.com/compute/v1/projects/strong-augury-224506/global/firewalls/allow-8000"
      },
      {
        "kind": "compute#firewall",
        "id": "4040737266126263753",
        "creationTimestamp": "2019-03-17T04:10:30.863-07:00",
        "name": "default-allow-http",
        "description": "",
        "network": "https://www.googleapis.com/compute/v1/projects/strong-augury-224506/global/networks/default",
        "priority": 1000,
        "sourceRanges": [
          "0.0.0.0/0"
        ],
        "targetTags": [
          "http-server"
        ],
        "allowed": [
          {
            "IPProtocol": "tcp",
            "ports": [
              "80"
            ]
          }
        ],
        "direction": "INGRESS",
        "logConfig": {
          "enable": false
        },
        "disabled": false,
        "selfLink": "https://www.googleapis.com/compute/v1/projects/strong-augury-224506/global/firewalls/default-allow-http"
      },
      {
        "kind": "compute#firewall",
        "id": "1042516550653023688",
        "creationTimestamp": "2019-03-17T04:10:31.620-07:00",
        "name": "default-allow-https",
        "description": "",
        "network": "https://www.googleapis.com/compute/v1/projects/strong-augury-224506/global/networks/default",
        "priority": 1000,
        "sourceRanges": [
          "0.0.0.0/0"
        ],
        "targetTags": [
          "https-server"
        ],
        "allowed": [
          {
            "IPProtocol": "tcp",
            "ports": [
              "443"
            ]
          }
        ],
        "direction": "INGRESS",
        "logConfig": {
          "enable": false
        },
        "disabled": false,
        "selfLink": "https://www.googleapis.com/compute/v1/projects/strong-augury-224506/global/firewalls/default-allow-https"
      },
      {
        "kind": "compute#firewall",
        "id": "4039603446308653022",
        "creationTimestamp": "2018-12-04T04:55:13.234-08:00",
        "name": "default-allow-icmp",
        "description": "Allow ICMP from anywhere",
        "network": "https://www.googleapis.com/compute/v1/projects/strong-augury-224506/global/networks/default",
        "priority": 65534,
        "sourceRanges": [
          "0.0.0.0/0"
        ],
        "allowed": [
          {
            "IPProtocol": "icmp"
          }
        ],
        "direction": "INGRESS",
        "logConfig": {
          "enable": false
        },
        "disabled": false,
        "selfLink": "https://www.googleapis.com/compute/v1/projects/strong-augury-224506/global/firewalls/default-allow-icmp"
      },
      {
        "kind": "compute#firewall",
        "id": "7698056288393742302",
        "creationTimestamp": "2018-12-04T04:55:13.164-08:00",
        "name": "default-allow-internal",
        "description": "Allow internal traffic on the default network",
        "network": "https://www.googleapis.com/compute/v1/projects/strong-augury-224506/global/networks/default",
        "priority": 65534,
        "sourceRanges": [
          "10.128.0.0/9"
        ],
        "allowed": [
          {
            "IPProtocol": "tcp",
            "ports": [
              "0-65535"
            ]
          },
          {
            "IPProtocol": "udp",
            "ports": [
              "0-65535"
            ]
          },
          {
            "IPProtocol": "icmp"
          }
        ],
        "direction": "INGRESS",
        "logConfig": {
          "enable": false
        },
        "disabled": false,
        "selfLink": "https://www.googleapis.com/compute/v1/projects/strong-augury-224506/global/firewalls/default-allow-internal"
      },
      {
        "kind": "compute#firewall",
        "id": "4897957599626643422",
        "creationTimestamp": "2018-12-04T04:55:13.211-08:00",
        "name": "default-allow-rdp",
        "description": "Allow RDP from anywhere",
        "network": "https://www.googleapis.com/compute/v1/projects/strong-augury-224506/global/networks/default",
        "priority": 65534,
        "sourceRanges": [
          "0.0.0.0/0"
        ],
        "allowed": [
          {
            "IPProtocol": "tcp",
            "ports": [
              "3389"
            ]
          }
        ],
        "direction": "INGRESS",
        "logConfig": {
          "enable": false
        },
        "disabled": false,
        "selfLink": "https://www.googleapis.com/compute/v1/projects/strong-augury-224506/global/firewalls/default-allow-rdp"
      },
      {
        "kind": "compute#firewall",
        "id": "6979850877904078814",
        "creationTimestamp": "2018-12-04T04:55:13.187-08:00",
        "name": "default-allow-ssh",
        "description": "Allow SSH from anywhere",
        "network": "https://www.googleapis.com/compute/v1/projects/strong-augury-224506/global/networks/default",
        "priority": 65534,
        "sourceRanges": [
          "0.0.0.0/0"
        ],
        "allowed": [
          {
            "IPProtocol": "tcp",
            "ports": [
              "22"
            ]
          }
        ],
        "direction": "INGRESS",
        "logConfig": {
          "enable": false
        },
        "disabled": false,
        "selfLink": "https://www.googleapis.com/compute/v1/projects/strong-augury-224506/global/firewalls/default-allow-ssh"
      }
    ]


### Group vs. Rule: Conclusion

Since GCP returns one JSON object per firewall rule while Azure groups
firewall rules under a network security group, the Azure cloud plugin
should generate multiple firewall rule records, one for each firewall
rule in a network security group in order to maintain parity with the
records of GCP firewall rules.

Further the `com` (common notation) bucket of the generated records
should be populated with generic attributes that are common in both
Azure and GCP firewall rules.

From the next section onwards, we will compare a single firewall rule,
i.e., a single item in the `security_rules` bucket in Azure with a
single `compute#firewall` document in GCP for various types of firewall
rules.


Multiple Ranges
---------------

There are primarily three important pieces of information we need to
analyze in a firewall rule to understand if the firewall rule is
potentially weak:

  - Direction: We will focus on inbound rules first. Azure calls it
    `Inbound`. GCP calls it `INGRESS`. In the common notation, we will
    write it as `in`.

  - Access: Rules can either deny access or allow access. We will focus
    only on those that allow access.

  - Source address range: We need to understand how they look in the
    JSON data when multiple ranges are defined. We want to find those
    rules that have `*` or `0.0.0.0/0` or any other pattern that
    indicates all source address ranges.

  - Destination port range: We need to understand how they look in the
    JSON data when multiple ranges are defined. If a range includes
    critical ports like 22 (SSH), 3389 (RDP), etc. then we want to check
    if they are allowed to all source address ranges.


### Multiple Ranges: Azure

Here is an example Azure security rule that allows multiple source
address ranges to connect to multiple destination port ranges:

    {
      "id": "/subscriptions/a8daa361-b951-46b5-8e9d-b375f2ffe9fd/resourceGroups/rg1/providers/Microsoft.Network/networkSecurityGroups/vm1-nsg/securityRules/Port_Multi_Range",
      "protocol": "*",
      "source_port_range": "*",
      "source_address_prefixes": [
        "122.178.216.0/24",
        "223.186.94.0/24"
      ],
      "destination_address_prefix": "*",
      "destination_address_prefixes": [],
      "source_port_ranges": [],
      "destination_port_ranges": [
        "8000",
        "8100-8200",
        "9000-9200"
      ],
      "access": "Allow",
      "priority": 330,
      "direction": "Inbound",
      "provisioning_state": "Succeeded",
      "name": "Port_Multi_Range",
      "etag": "W/\"1bc610ec-1b0b-4410-8574-375ba7842653\""
    }


### Multiple Ranges: GCP

Here is a similar GCP rule that allows multiple source address ranges to
connect to multiple destination port ranges:

    {
      "kind": "compute#firewall",
      "id": "5234693571178890345",
      "creationTimestamp": "2019-03-17T05:16:06.712-07:00",
      "name": "allow-multi-range",
      "description": "",
      "network": "https://www.googleapis.com/compute/v1/projects/strong-augury-224506/global/networks/default",
      "priority": 1000,
      "sourceRanges": [
        "122.178.216.0/24",
        "223.186.94.0/24"
      ],
      "allowed": [
        {
          "IPProtocol": "tcp",
          "ports": [
            "8000",
            "8100-8200",
            "9000-9200"
          ]
        },
        {
          "IPProtocol": "udp",
          "ports": [
            "5000",
            "5100-5200",
            "6000-6200"
          ]
        }
      ],
      "direction": "INGRESS",
      "logConfig": {
        "enable": false
      },
      "disabled": false,
      "selfLink": "https://www.googleapis.com/compute/v1/projects/strong-augury-224506/global/firewalls/allow-multi-range"
    }

**Note:** One can argue that this GCP firewall rule is not exactly
similar to the Azure security rule because the Azure security rule has
`protocol` as `*` whereas this GCP firewall rule has two different sets
of port ranges for `tcp` and `udp`. That's true, however, we are not
trying to understand how the protocol appears in a firewall rule. We are
only trying to understand the various formats of source address ranges
and destination port ranges in this section. We will discuss the
protocol format in a different section.

For the record, the reason why the protocol is `*` in Azure is that
while creating a security rule in Azure portal, the options for protocol
is "Any", "TCP", and "UDP", with "Any" as the default. There is no such
"Any" option while creating GCP firewall rule. GCP Console requires us
to enter port ranges separately for TCP and UDP protocols. There is an
"Allow all" option in GCP that allows connections via all protocols and
connections to all ports. Azure's "Any" option, on the other hand,
allows connections to specified ports only via both protocols.


### Multiple Ranges: Conclusion

Detecting whether a sensitive port, say port 22 or port 3389, is exposed
to the Internet we first need to check if these ports exist in the port
ranges. For the minimum viable product, we can do a straightforward
string match and see if `22` or `3389` explicitly exists in the list of
port ranges.

For a more complete implementation, we would also have to check if `22`
or `3389` exists within a port range, e.g., `20-40` or `3000-4000`.


Any Source
----------

In the initial phase of this project, we are only worried about source
address ranges that allow all source addresses to connect to the
specified port ranges.


### Any Source: Azure

There are many different ways a specific port can be exposed to the
entire Internet, so we divide this subsection into further subsections.


#### `source_address_prefix set` to `*`

When an inbound security rule is configured with `Any` chosen from the
*Source* dropdown, we get a JSON object like this from Azure with
`source_address_prefix` set to `*`:

    {
      "id": "/subscriptions/a8daa361-b951-46b5-8e9d-b375f2ffe9fd/resourceGroups/rg1/providers/Microsoft.Network/networkSecurityGroups/vm1-nsg/securityRules/Port_22",
      "protocol": "*",
      "source_port_range": "*",
      "destination_port_range": "22",
      "source_address_prefix": "*",
      "source_address_prefixes": [],
      "destination_address_prefix": "*",
      "destination_address_prefixes": [],
      "source_port_ranges": [],
      "destination_port_ranges": [],
      "access": "Allow",
      "priority": 100,
      "direction": "Inbound",
      "provisioning_state": "Succeeded",
      "name": "Port_22",
      "etag": "W/\"c0acb806-e574-48c5-ad6a-d1f99f25d0ff\""
    }


#### `source_address_prefix` set to CIDR

Azure portal did not allow us to configure a rule with source address
range as `0.0.0.0/0`. An attempt to do so resulted in this error:
`Invalid argument: 'Prefix'. Reason: The prefix must be between 1 and 32.`.

The weakest rule we could configure in Azure portal using a single CIDR
is `0.0.0.0/1`. When an inbound security rule is configured with `IP
Addresses` chosen from the *Source* dropdown and *Source IP
addresses/CIDR ranges* set to `0.0.0.0/1`, we get a JSON object like
this with `source_address_prefix` set to the specified CIDR:

    {
      "id": "/subscriptions/a8daa361-b951-46b5-8e9d-b375f2ffe9fd/resourceGroups/rg1/providers/Microsoft.Network/networkSecurityGroups/vm1-nsg/securityRules/Port_22",
      "protocol": "*",
      "source_port_range": "*",
      "destination_port_range": "22",
      "source_address_prefix": "0.0.0.0/1",
      "source_address_prefixes": [],
      "destination_address_prefix": "*",
      "destination_address_prefixes": [],
      "source_port_ranges": [],
      "destination_port_ranges": [],
      "access": "Allow",
      "priority": 100,
      "direction": "Inbound",
      "provisioning_state": "Succeeded",
      "name": "Port_22",
      "etag": "W/\"49c2141f-8788-409f-a8a6-59005f246067\""
    }

Although Azure portal does not allow `0.0.0.0/0` as a CIDR range for
`source_address_prefix`, we should still check for it to be extra safe.
It may be possible that there are other means (such as a direct REST API
call) to set `source_address_prefix` to `0.0.0.0/0`.

TODO: Check if we have any data that shows `source_address_prefix` set
to `0.0.0.0/0`.


#### `source_address_prefixes` set to multiple CIDRs

Here is yet another way to allow the entire Internet to allow access to
port 22. This security rule was created by choosing `IP Addresses` from
the *Source* dropdown and setting *Source IP addresses/CIDR ranges* as
`0.0.0.0/1,128.0.0.0/1`.

    {
      "id": "/subscriptions/a8daa361-b951-46b5-8e9d-b375f2ffe9fd/resourceGroups/rg1/providers/Microsoft.Network/networkSecurityGroups/vm1-nsg/securityRules/Port_22",
      "protocol": "*",
      "source_port_range": "*",
      "destination_port_range": "22",
      "source_address_prefixes": [
        "0.0.0.0/1",
        "128.0.0.0/1"
      ],
      "destination_address_prefix": "*",
      "destination_address_prefixes": [],
      "source_port_ranges": [],
      "destination_port_ranges": [],
      "access": "Allow",
      "priority": 100,
      "direction": "Inbound",
      "provisioning_state": "Succeeded",
      "name": "Port_22",
      "etag": "W/\"7a502bb3-4f77-4c23-a5fa-1fda055b021d\""
    }

To take multiple CIDRs into account and finding whether their union is
`0.0.0.0/0` should perhaps be outside the scope of MVP. Detecting this
kind of corner cases (which could be exploited as clever workarounds to
avoid detection) should perhaps be picked up as enhancements later.

Until then, perhaps to be extra safe we should look for `0.0.0.0/0` as
one of the entries in the `source_address_prefixes` list.


#### `source_address_prefix` set to `Internet`

Yet another way to expose a port to the Internet is to use a service
tag. This security rule was created by choosing `Service Tag` from the
*Source* dropdown menu and choosing `Internet` from the *Source service
tag* dropdown menu.

    {
      "id": "/subscriptions/a8daa361-b951-46b5-8e9d-b375f2ffe9fd/resourceGroups/rg1/providers/Microsoft.Network/networkSecurityGroups/vm1-nsg/securityRules/Port_22",
      "protocol": "*",
      "source_port_range": "*",
      "destination_port_range": "22",
      "source_address_prefix": "Internet",
      "source_address_prefixes": [],
      "destination_address_prefix": "*",
      "destination_address_prefixes": [],
      "source_port_ranges": [],
      "destination_port_ranges": [],
      "access": "Allow",
      "priority": 100,
      "direction": "Inbound",
      "provisioning_state": "Succeeded",
      "name": "Port_22",
      "etag": "W/\"a9ceecf4-eb4a-4fb8-b961-4cd9ab246e70\""
    }


### Any Source: GCP

GCP contains this firewall rule to allow connectivity to port 22 from
anywhere as one of the default rules:

    {
      "kind": "compute#firewall",
      "id": "6979850877904078814",
      "creationTimestamp": "2018-12-04T04:55:13.187-08:00",
      "name": "default-allow-ssh",
      "description": "Allow SSH from anywhere",
      "network": "https://www.googleapis.com/compute/v1/projects/strong-augury-224506/global/networks/default",
      "priority": 65534,
      "sourceRanges": [
        "0.0.0.0/0"
      ],
      "allowed": [
        {
          "IPProtocol": "tcp",
          "ports": [
            "22"
          ]
        }
      ],
      "direction": "INGRESS",
      "logConfig": {
        "enable": false
      },
      "disabled": false,
      "selfLink": "https://www.googleapis.com/compute/v1/projects/strong-augury-224506/global/firewalls/default-allow-ssh"
    }

Here is a how a custom firewall rule to allow connectivity to TCP port
8000 with *Source IP ranges* as `0.0.0.0/0` looks like:

    {
      "kind": "compute#firewall",
      "id": "4387657442666205482",
      "creationTimestamp": "2019-03-26T11:30:29.863-07:00",
      "name": "allow-8000",
      "description": "",
      "network": "https://www.googleapis.com/compute/v1/projects/strong-augury-224506/global/networks/default",
      "priority": 1000,
      "sourceRanges": [
        "0.0.0.0/0"
      ],
      "allowed": [
        {
          "IPProtocol": "tcp",
          "ports": [
            "8000"
          ]
        }
      ],
      "direction": "INGRESS",
      "logConfig": {
        "enable": false
      },
      "disabled": false,
      "selfLink": "https://www.googleapis.com/compute/v1/projects/strong-augury-224506/global/firewalls/allow-8000"
    }


### Any Source: Conclusion

Once we have identified that a critical port such as `22` or `3389` is
exposed as discussed in the previous section, we need to identify if it
is exposed to the entire Internet.

In this section, we learn that checking this is not straightforward in
case of Azure. There are multiple formats to consider in Azure:

  - `"source_address_prefix": "*"`
  - `"source_address_prefix": "0.0.0.0/0"`
  - `"source_address_prefix": "Internet"`
  - `"source_address_prefixes"` list contains `0.0.0.0/0`

With GCP, it is straightforward due to its simple UI and simple JSON
structure. We only need to check if `0.0.0.0/0` exists in
`sourceRanges`.

One way to simplify this in Azure could be to normalize the source
ranges in `source_address_prefix` and `source_address_prefixes` to a
common format, e.g., normalize both `*` and `Internet` to `0.0.0.0/0`
and let the source ranges in the common (`com`) bucket be always a list
even if a single address in `source_address_prefix` is available.


Any Port
--------

### Any Port: Azure

In this section, we will see a couple of ways we can allow connections
to all ports.


#### `destination_port_range` set to `*`

Here is an example where an inbound security rule is created with
*Destination port ranges* set to `*`.

    {
      "id": "/subscriptions/a8daa361-b951-46b5-8e9d-b375f2ffe9fd/resourceGroups/rg1/providers/Microsoft.Network/networkSecurityGroups/vm1-nsg/securityRules/Port_All",
      "protocol": "*",
      "source_port_range": "*",
      "destination_port_range": "*",
      "source_address_prefix": "*",
      "source_address_prefixes": [],
      "destination_address_prefix": "*",
      "destination_address_prefixes": [],
      "source_port_ranges": [],
      "destination_port_ranges": [],
      "access": "Allow",
      "priority": 100,
      "direction": "Inbound",
      "provisioning_state": "Succeeded",
      "name": "Port_All",
      "etag": "W/\"2873cc0e-f41e-4c94-a773-8b0982d0db55\""
    }


#### `destination_port_range` set to integer range

Another way to allow access to all ports is to set *Destination port
ranges* to `0-65535`.

    {
      "id": "/subscriptions/a8daa361-b951-46b5-8e9d-b375f2ffe9fd/resourceGroups/rg1/providers/Microsoft.Network/networkSecurityGroups/vm1-nsg/securityRules/Port_All",
      "protocol": "*",
      "source_port_range": "*",
      "destination_port_range": "0-65535",
      "source_address_prefix": "*",
      "source_address_prefixes": [],
      "destination_address_prefix": "*",
      "destination_address_prefixes": [],
      "source_port_ranges": [],
      "destination_port_ranges": [],
      "access": "Allow",
      "priority": 100,
      "direction": "Inbound",
      "provisioning_state": "Succeeded",
      "name": "Port_All",
      "etag": "W/\"50b4dbec-2e8e-4675-b050-f87017d5118c\""
    }


#### `destination_port_ranges` set to multiple integer ranges

If multiple port ranges are specified, they occur as a list value of
the `destination_port_ranges` key as opposed to a single scalar value of
the `destination_port_range` key. Note that this is very similar to
`source_address_prefix` and `source_address_prefixes` that we saw
earlier.

For example, this security rule was created by setting *Destination port
ranges* to `0-1024,1025-50000,50001-65535`.

    {
      "id": "/subscriptions/a8daa361-b951-46b5-8e9d-b375f2ffe9fd/resourceGroups/rg1/providers/Microsoft.Network/networkSecurityGroups/vm1-nsg/securityRules/Port_All",
      "protocol": "*",
      "source_port_range": "*",
      "source_address_prefix": "*",
      "source_address_prefixes": [],
      "destination_address_prefix": "*",
      "destination_address_prefixes": [],
      "source_port_ranges": [],
      "destination_port_ranges": [
        "0-1024",
        "1025-50000",
        "50001-65535"
      ],
      "access": "Allow",
      "priority": 110,
      "direction": "Inbound",
      "provisioning_state": "Succeeded",
      "name": "Port_All",
      "etag": "W/\"7f844086-b511-4fbb-b14e-67bb6d1635a6\""
    }


An interesting thing worth figuring out was if we could create a rule
with *Destination port ranges* as something like `*,8080` to know if we
need to handle formats like these as well. An attempt to create a rule
like this failed with this error: `Error: Security rule parameter
DestinationPortRanges for rule with Id vm1-nsg/Port_All_8080 cannot
specify a '*'`.


### Any Port: GCP

#### `ports` missing

Here is an example of a firewall rule created by setting *Protocol and
ports* to `Allow all`.

    {
      "kind": "compute#firewall",
      "id": "3768982350163559256",
      "creationTimestamp": "2019-03-26T21:36:07.919-07:00",
      "name": "allow-all",
      "description": "",
      "network": "https://www.googleapis.com/compute/v1/projects/strong-augury-224506/global/networks/default",
      "priority": 1000,
      "sourceRanges": [
        "0.0.0.0/0"
      ],
      "allowed": [
        {
          "IPProtocol": "all"
        }
      ],
      "direction": "INGRESS",
      "logConfig": {
        "enable": false
      },
      "disabled": false,
      "selfLink": "https://www.googleapis.com/compute/v1/projects/strong-augury-224506/global/firewalls/allow-all"
    }

Here is an example of another firewall rule created by setting *Protocol
and ports* to `Specified protocols and ports` with only `tcp` checked
and its value set to `all`.

    {
      "kind": "compute#firewall",
      "id": "4743040530138971883",
      "creationTimestamp": "2019-03-26T21:45:56.996-07:00",
      "name": "allow-tcp-all",
      "description": "",
      "network": "https://www.googleapis.com/compute/v1/projects/strong-augury-224506/global/networks/default",
      "priority": 1000,
      "sourceRanges": [
        "0.0.0.0/0"
      ],
      "allowed": [
        {
          "IPProtocol": "tcp"
        }
      ],
      "direction": "INGRESS",
      "logConfig": {
        "enable": false
      },
      "disabled": false,
      "selfLink": "https://www.googleapis.com/compute/v1/projects/strong-augury-224506/global/firewalls/allow-tcp-all"
    }

We see in both examples above that the absence of `ports` key within an
item under `allowed` key means access to all ports is allowed. To
confirm that `ports` key exist otherwise, see the next example.


#### `ports` set to port range

Here is another example that allows access to all ports by setting the
port range for TCP to `0-65535`

    {
      "kind": "compute#firewall",
      "id": "1643439697213955467",
      "creationTimestamp": "2019-03-27T00:16:52.999-07:00",
      "name": "allow-0-65535",
      "description": "",
      "network": "https://www.googleapis.com/compute/v1/projects/strong-augury-224506/global/networks/default",
      "priority": 1000,
      "sourceRanges": [
        "0.0.0.0/0"
      ],
      "allowed": [
        {
          "IPProtocol": "tcp",
          "ports": [
            "0-65535"
          ]
        }
      ],
      "direction": "INGRESS",
      "logConfig": {
        "enable": false
      },
      "disabled": false,
      "selfLink": "https://www.googleapis.com/compute/v1/projects/strong-augury-224506/global/firewalls/allow-0-65535"
    }

If the `ports` key exist within an item in the list value of `allowed`
key, then we need to see if the port we care about is present in it.

Specifying a port range like `8000,all,9000-9010` in the GCP console
fails with this error: `'all' has to be the only value provided`.


### Any Port: Conclusion

Normally, we would not care that a firewall rule exposes all ports. We
would only care whether specific sensitive ports like 22, 3389, etc. are
exposed. But still the understanding of how the notion of all ports is
denoted in the data is important because if all ports are exposed, then
the ports we care about such as 22 or 3389 are also exposed.

In Azure, we need to consider the following special formats:

  - `"destination_port_range": "*"` (allows all ports)
  - `"destination_port_range"` is set to the port we care about.
  - `"destination_port_range"` is a port range that contains the port we
    care about.
  - `"destination_port_ranges"` list contains the port we care about.
  - `"destination_port_ranges"` list contains a port range that contains
    the port we care about.

In GCP, we need to consider the following cases:

  - `"allowed"` list contains an object literal with the `"ports"` key
    missing (thus allowing all ports).
  - `"allowed"` list contains an object literal that contains the port
    we care about.
  - `"allowed"` list contains an object literal that contains a port
    range that contains the port we care about.

One way to simplify all these various scenarios is to use a list of
port/port-ranges as common notation. So `*` would be normalized to
`0-65535`. A missing `"ports"` key is also normalized to `0-65535`.
