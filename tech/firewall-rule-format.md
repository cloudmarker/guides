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

* [Group vs. Rule](#group-vs-rule)
* [Multiple Ranges](#multiple-ranges)
* [Any Source](#any-source)


Group vs. Rule
--------------

### Azure

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


### GCP

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
    indicates *all* source address ranges.

  - Destination port range: We need to understand how they look in the
    JSON data when multiple ranges are defined. If a range includes
    critical ports like 22 (SSH), 3389 (RDP), etc. then we want to check
    if they are allowed to all source address ranges.


### Azure

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


### GCP

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
to enter port ranges separately for TCP and UDP protocols.


Any Source
----------

In the initial phase of this project, we are not worried about firewall
rules with well defined source address ranges. But we are worried about
a source address range that allows all source addresses to connect to
the specified port ranges.

***Coming up!***
