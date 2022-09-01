---

title: "Transfer Digital Credentials Securely - Requirements"
abbrev: "tigress-requirements"
category: info

docname: draft-tigress-requirements-latest
submissiontype: IETF
number:
date:
consensus: true
v: 3
# area: AREA
# workgroup: TIGRESS
keyword: TODO
venue:
#  group: WG
#  type: Working Group
#  mail: WG@example.com
#  arch: https://example.com/WG
  github: "dimmyvi/tigress-requirements"
  latest: "https://dimmyvi.github.io/tigress-requirements/draft-tigress-requirements.html"

author:
 -
    ins: D. Vinokurov
    name: Dmitry Vinokurov
    organization: Apple Inc
    email: dvinokurov@apple.com
 -
    ins: C. Astiz
    name: Casey Astiz
    organization: Apple Inc
    email: mbyington@apple.com
 -
    ins: A. Pelletier
    name: Alex Pelletier
    organization: Apple Inc
    email: a_pelletier@apple.com
 -
    ins: J.L. Giraud
    name: Jean-Luc Giraud
    organization: Apple Inc
    email: jgiraud@apple.com
 -
    ins: A. Bulgakov
    name: Alexey Bulgakov
    organization: Apple Inc
    email: abulgakov@apple.com
 -
    ins: N. Sha
    name: Nick Sha
    organization: Alphabet Inc
    email: nicksha@google.com


informative:


--- abstract

This document describes use cases for transfer digital credentials securely proposal, defines requirements and the scope.


--- middle

# Introduction

TODO Introduction


# Conventions and Definitions

{::boilerplate bcp14-tagged}

General terms:

- Credential information - data used to authenticate the user with an access point.
- Provisioning information* - data transferred from Sender to Receiver device that is both necessary and sufficient for the Receiver to request a new credential from Provisioning Partner to provision it to the Receiver device.
- Provisioning - A process of adding a new credential to the device.
- Provisioning Partner - an entity which facilitates Credential Information lifecycle on a device. Lifecycle may include provisioning of credential, credential termination, credential update.
- Sender (device) - a device initiating a transfer of Provisioning Information to a Receiver that can provision this credential.
- Receiver (device) - a device that receives Provisioning Information and uses it to provision a new credential.
- Intermediary (server) - an intermediary server that facilitates transfer of provisioning information between Sender and Receiver.

# Use Cases

## 1. Let's say Ben owns a vehicle that supports digital keys which comply with the CCC open standard. Ben would like to let Ryan borrow the car for the weekend. Ryan and Ben are using two different mobile phones with different operating systems. In order for Ben to share his car key to Ryan for a weekend, he must transfer some data to the receiver device. The data structure shared between the two participants is defined in the CCC. In addition, the CCC requires the receiver to generate required key material and return it to the sender to sign and return back to the receiver. At this point, the receiver now has a token that will allow them to provision their new key with the car.

## 2. Bob booked a room at a hotel for the weekend, but will be arriving late at night . Alice, his partner, comes to the hotel first, so Bob wants to share his key to the room with Alice. Bob and Alice are using two different mobile phones with different operating systems. In order for Bob to share his key to the hotel to Alice for a weekend, he must transfer some data to the her device. The data structure shared between the two participants is proprietary to the given hotel chain (or Provisioning Partner). This data transfer is a one-time, unidirectional from Bob’s device to Alice’s. Once Alice receives this data, she can provision a new pass to her device, making a call to Provisioning Partner to receive a new credential.

# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
