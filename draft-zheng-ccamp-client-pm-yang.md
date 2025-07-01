---
title: "A YANG Data Model for Client Signal Performance Monitoring"
abbrev: "PM YANG for Client Signal"
category: std

docname: draft-zheng-ccamp-client-pm-yang-latest
submissiontype: IETF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
area: "Routing"
workgroup: "CCAMP Working Group"
venue:
  group: "Common Control and Measurement Plane"
  type: "Working Group"
  mail: "ccamp@ietf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/ccamp/"
  github: "haomianzheng/ccamp-client-pm-yang"
  latest: "https://haomianzheng.github.io/ccamp-client-pm-yang/draft-zheng-ccamp-client-pm-yang.html"

author:
  -
    name: Chaode Yu
    org: Huawei Technologies
    email: yuchaode@huawei.com
  -
    name: Haomian Zheng
    org: Huawei Technologies
    street: H1, Huawei Xiliu Beipo Village, Songshan Lake
    city: Dongguan
    region: Guangdong
    code: 523808
    country: China
    email: zhenghaomian@huawei.com
  -
    name: Italo Busi
    org: Huawei Technologies
    country: Italy
    email: italo.busi@huawei.com
  -
    name: Yanlei Zheng
    org: China Unicom
    country: China
    email: zhengyanlei@chinaunicom.cn
  -
    name: Victor Lopez
    org: Nokia
    country: Spain
    email: victor.lopez@nokia.com
  -
    name: Oscar Gonzalez de Dios
    ins: O. Gonzalez de Dios
    org: Telefonica
    country: Spain
    email: oscar.gonzalezdedios@telefonica.com

#contributor:

normative:
  ITU-T_G.709:
    title: Interfaces for the optical transport network
    author:
      org: International Telecommunication Union
    date: June 2020
    seriesinfo: ITU-T Recommendation G.709
  ITU-T_G.7710:
    title: Common equipment management function requirements
    author:
      org: International Telecommunication Union
    date: October 2020
    seriesinfo: ITU-T Recommendation G.7710
  ITU-T_G.7718:
    title: Framework for the management of management-control components and functions
    author:
      org: International Telecommunication Union
    date: October 2020
    seriesinfo: ITU-T Recommendation G.7718
  ITU-T_G.7719:
    title: Management information model for management-control components and functions
    author:
      org: International Telecommunication Union
    date: June 2021
    seriesinfo: ITU-T Recommendation G.7719
  ITU-T_G.874:
    title: Management aspects of optical transport network elements
    author:
      org: International Telecommunication Union
    date: November 2022
    seriesinfo: ITU-T Recommendation G.874
  ITU-T_G.875:
    title: Optical transport network: Protocol-neutral management information model for the network element view
    author:
      org: International Telecommunication Union
    date: June 2020
    seriesinfo: ITU-T Recommendation G.875

informative:

--- abstract

A transport network is a server-layer network to provide connectivity services to its client.  Given the client signal is configured, the followup function for performance monitoring, such as latency and bit error rate, would be needed for network operation.

This document describes the data model to support the performance monitoring functionalities.

--- middle

# Introduction

Client-layer network and server-layer network have been respectively modeled to allow the tunnels carrying the client traffic.  Server-layers are modeled as tunnels with various switching technologies, such as OTN in {{?I-D.ietf-ccamp-otn-tunnel-model}} and WDM in {{?I-D.ietf-ccamp-wdm-tunnel-yang}}.  Client-layers are modeled as client signals according to the client-signal identities specified in {{!I-D.ietf-ccamp-layer1-types}}.  These client signals can be configured to existing tunnels via the client signal configuration model specified in {{!I-D.ietf-ccamp-client-signal-yang}}.

In the network operation, the operator is interested in monitoring their instantiated client signal over tunnels.  The objective of such monitoring is to complete timely adjustment once there is abnormal statistic which may result in failure of the client signal.  The parameters specified in the performance monitoring model can be collected for the operation need.  The OAM mechanism, can be configured together with the performance monitoring model.

# Terminology and Notations

A simplified graphical representation of the data model is used in this document.  The meaning of the symbols in the YANG data tree presented later in this document is defined in {{?RFC8340}}.  They are provided below for reference.

- Brackets "\[" and "]" enclose list keys.

- Abbreviations before data node names: "rw" means configuration
  (read-write) and "ro" state data (read-only).

- Symbols after data node names: "?" means an optional node, "!"
  means a presence container, and "*" denotes a list and leaf-list.

- Parentheses enclose choice and case nodes, and case nodes are also
  marked with a colon (":").

- Ellipsis ("...") stands for contents of subtrees that are not
  shown.

# Model Relationship

{{!I-D.ietf-ccamp-client-signal-yang}} has specified the two models for the client signal configuration, module ietf-trans-client-service for transparent client service and module ietf-eth-tran-service for Ethernet service.  Basically the client signal types in this document is consistent with ietf-eth-tran-types, and focus on different functionality.  On the perspective of operator, the modules in {{!I-D.ietf-ccamp-client-signal-yang}} can be used to configure the service given any underlay tunnels, while the operation about monitoring the performance on given service can be achieved by using the model in this document.

Consideration on Key Performance Information (KPI) monitoring for Virtual Network (VN) and tunnels has been specified in {{!I-D.ietf-teas-actn-pm-telemetry-autonomics}}.  Usually the monitoring on the tunnels are the VNs should be separately deployed for the network operation, but it is possible to have common parameters that are both needed for the VN/TE and the configured services.  Common types are imported in both modules.

VPN-level parameters and their monitoring have been defined in {{!I-D.www-bess-yang-vpn-service-pm}}.  This module focus on the performance on the topology at different layer or the overlay topology between VPN sites.  On the other hand, this document is focusing on the performance of the service configured between Customer Ends (CE).

{{!I-D.yu-ccamp-optical-resource-pm-yang}} is aimed to provide a performance management approach on the resource level in a traditional way. This resource stands for physical resource, such as board, port etc., or logical resource, e.g. TTP etc. The management object is different with this document. But there is some relationship between these two documents. The PM data of client signal can be collected on some specific resources.
And usually there would be some additional calculation needed for client signal PM data.
This collection mechanism, including which resource should be adopted, when these resource PM data should be collected and the calculation method are focus of this document.

# Use Cases of Performance Management of Client Signal

## Automatic Service Acceptance Test

After the private line service is provisioned on the network, usually it needs to take an acceptance test before it is delivered to the user.
This acceptance test includes some connectivity validation, such as traffic test, to make sure that it's reachable from the source access port to the destination access port. The engineers need to take tester onsite and run it manually.
It is time consuming especially when the private line service is an inter-domain service which the source and destination could be in different cities.

It is excellent if this acceptance test can be operated automatically by interface instead of being done manually.
For some scenarios, it is feasible to achieve this target.
For example, we can test the latency of private line service to replace the connectivity test.
The section of 15.8.2.1.6 in {{ITU-T_G.709}} defines the mechanism of delay (latency) measurement mechanism of ODU path. If the latency value could be returned successfully through the ODU path, then there will not be interruption on the ODU path.

## Private Line Service SLA Assurance

SLA (Service Level Agreement) is an agreement aligned by the service provider and the user. This agreement defines service type, quality of service etc. which the service provider guarantees to the user.

Transport private line service has got the advantage of hard isolation, large bandwidth, low latency and high reliability. So usually it is more expensive than the other fixed broadband services. From the user's perspective, they also have some special demand for the private line service. For example, some industry customers, e.g. stock and futures industry customers who have a lot of high-frequency trading requirement, have extremely high requirement on latency. The customer from government and security assurance department have extremely high requirements on service reliability. The Private line service users expect to monitor service performance indicators to ensure that their private line services are cost-effective and meet SLA requirements.

And for the service provider, continuous monitoring of key services' performance and proactive O&M can reduce customers' complaint and ensure SLA delivery. The performance data can even be used for precision marketing. For example, if the bandwidth usage of a user's private line is too high for a long time, the system can remind the user to adjust the bandwidth in a timely manner.

~~~~ ascii-art
 +----------+   +----------+
 | Operator |   |   User   |
 +----------+   +----------+
      ||             ||
 +----------+   +----------+
 |   OSS    |   |   APP    |
 +----------+   +----------+
      ||             ||
  +-----------------------+
  |  domain Controller    |
  +-----------------------+
            ||
      --------------
     /    Network   \
     \______________/

~~~~
{: #fig-sla-assurance title="Architecture of Private Line SLA Assurance"}

# Consideration on Monitoring Parameters

For the mechanism of performance monitoring, there have been a lot of discussion in {{ITU-T_G.709}}, {{ITU-T_G.874}}, {{ITU-T_G.875}}, {{ITU-T_G.7710}}, {{ITU-T_G.7718}}, and {{ITU-T_G.7719}}.
This document would like to reference the definition of ITU-T instead of restarting new discussion. But for the service level's performance parameter, there is not enough definition both in ITU-T and IETF, this document will focus on how to define a service level performance parameter.
Considering there could be a lot of new service performance parameters in the future, it is also suggested to define a generic data model to conduct the service performance parameters.

## Service Latency Measurement

According to the description of section 15.8.2.1.6 in {{ITU-T_G.709}}, PM overhead can be used to measure the delay (latency) of ODU path. Simply speaking, in the latency measurement process, the PM overhead is generated and delivered on the source port and looped back at the sink port. By observing the 0-1 change of PM overhead on the source port, it is able to obtain latency data of E2E ODU path.

For intra-domain services, the domain controller can differentiate who is the source port and who is the sink port, and orchestrate the whole measurement  process. But for inter-domain service, it is hard for the domain controller to know the access port in its domain is a source or sink port. Therefore an orchestrator above is needed to do this orchestration. The orchestrator specify one of the domain's access port performs as the sink port and the other domain's access port performs as a source port. To be noted that, it is important specify the source and sink ports. Especially the sink port should be specified at first. It is not allowed to launch latency measurement from the source port until the sink port has finished its configuration (loop-back operation). Otherwise the overhead will not be transmitted back to the source port, so that no latency data will be obtained.

~~~~ ascii-art
               +--------------+
               | Orchestrator |
               +--------------+
               /       |      \
              /        |       \
             /         |        \
            /          |         \
      +------+     +------+     +------+
      | DC-A |     | DC-N |     | DC-Z |
      +------+     +------+     +------+
         |             |            |
  /-----------\        |        /-----------\
  | Network-A | ---(domian-n)---| Network-Z |--+
  |           |                 |           |<-|  loopback
  \-----------/                 \-----------/
~~~~
{: #fig-inter-domain-latency title="Inter-domain service latency measurement"}

# OAM Configuration

The operation, administration and maintenance protocols and data models have been specified in {{!RFC8531}} for the connection-oriented network.  The model is referenced in this work to develop an Ethernet-specific OAM models, which is augmenting the service performance monitoring data model.

The definitions of OAM terminologies, such as Maintenance Domain (MD), Maintenance Association (MA), and Maintenance End Points (MEP), can be found in {{!RFC8531}} as well.

# YANG Model for Performance Monitoring

## YANG Tree for Performance Monitoring

~~~~ ascii-art
{::include ietf-service-pm.tree}
~~~~
{: #fig-service-pm-tree title="YANG Tree for Performance Monitoring" artwork-name="ietf-service-pm.tree"}

## YANG Tree for OAM Configuration

~~~~ ascii-art
{::include ietf-eth-service-oam.tree}
~~~~
{: #fig-eth-service-oam-tree title="YANG Tree for OAM Configuration" artwork-name="ietf-eth-service-oam.tree"}

# YANG Code for Performance Monitoring

## The Performance Monitoring YANG Code

~~~~ yang
{::include ietf-service-pm.yang}
~~~~
{: #fig-service-pm-yang title="Performance Monitoring YANG Code"
sourcecode-markers="true" sourcecode-name="ietf-service-pm@2021-07-07.yang"}

## The OAM Configuration YANG Code

~~~~ yang
{::include ietf-eth-service-oam.yang}
~~~~
{: #fig-eth-service-oam-yang title="OAM Configuration YANG Code"
sourcecode-markers="true" sourcecode-name="ietf-eth-service-oam@2024-03-04.yang"}

# IANA Considerations

This document requests IANA to register the following URIs in the "ns" subregistry within the "IETF XML Registry" {{!RFC3688}}. Following the format in {{!RFC3688}}, the following registrations are requested.

~~~~
      URI: urn:ietf:params:xml:ns:yang:ietf-service-pm
      Registrant Contact: The IESG
      XML: N/A; the requested URI is an XML namespace.

      URI: urn:ietf:params:xml:ns:yang:ietf-eth-service-oam
      Registrant Contact: The IESG
      XML: N/A; the requested URI is an XML namespace.
~~~~

This document requests IANA to register the following YANG modules in the "IANA Module Names" {{!RFC6020}}. Following the format in {{!RFC6020}}, the following registrations are requested:

~~~~
      name:         ietf-service-pm
      namespace:    urn:ietf:params:xml:ns:yang:ietf-service-pm
      prefix:       svc-pm
      reference:    RFC XXXX (This document)

      name:         ietf-eth-service-oam
      namespace:    urn:ietf:params:xml:ns:yang:ietf-eth-service-oam
      prefix:       eth-oam
      reference:    RFC XXXX (This document)
~~~~

RFC Editor: Please replace XXXX with the RFC number assigned to this document.

# Manageability Considerations

TBD.

# Security Considerations

The data following the model defined in this document is exchanged
via, for example, the interface between an orchestrator and a
transport network controller.  The security concerns mentioned in
{{!I-D.ietf-ccamp-client-signal-yang}} also applies to this document.

The YANG module defined in this document can be accessed via the
RESTCONF protocol defined in {{!RFC8040}}, or maybe via the NETCONF
protocol {{!RFC6241}}.

--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
