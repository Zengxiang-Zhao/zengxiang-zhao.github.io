---
layout: default
title: AWS Exam SAP-CO2
date:   2021-09-11 20:28:05 -0400
categories: Linux
nav_order: 101

---

---
{: .no_toc }

<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

# TODO

1. How to create a Hub-and-Spoke Topologies. Use the tutorial to build a real structure in the AWS to understand the concepts of VPC, Subnet, AWS Transit Gate Way and CIDR blocks, EC2, Security groups, Loading balancer, etc.

# The relationship between AWS Region, AWS AZs, VPC and subnets

The following example comes from DeepSeek.

## Visualizing the Relationship

```bash
AWS Region: us-east-1 (N. Virginia)
│
├── Availability Zone: us-east-1a
│   │
│   └── VPC: vpc-12345 (CIDR: 10.0.0.0/16)
│       │
│       ├── Subnet A (10.0.1.0/24) --> (Lives ONLY in us-east-1a)
│       └── Subnet B (10.0.2.0/24) --> (Lives ONLY in us-east-1a)
│
├── Availability Zone: us-east-1b
│   │
│   └── VPC: vpc-12345 (Same VPC as above)
│       │
│       ├── Subnet C (10.0.3.0/24) --> (Lives ONLY in us-east-1b)
│       └── Subnet D (10.0.4.0/24) --> (Lives ONLY in us-east-1b)
│
└── Availability Zone: us-east-1c
    │
    └── VPC: vpc-12345 (Same VPC as above)
        │
        ├── Subnet E (10.0.5.0/24) --> (Lives ONLY in us-east-1c)
        └── Subnet F (10.0.6.0/24) --> (Lives ONLY in us-east-1c)

```

## Why this matters (Networking & High Availability)

You cannot put a subnet in two AZs. If you want an application to survive an AZ failure (e.g., a power outage in us-east-1a), you must:

Create two subnets in the same VPC.

Place Subnet 1 in us-east-1a.

Place Subnet 2 in us-east-1b.

Launch EC2 Instance A in Subnet 1.

Launch EC2 Instance B in Subnet 2.

**Put an ELB (Load Balancer) in front of both.**

If AZ a dies, traffic routes to Instance B in AZ b.
