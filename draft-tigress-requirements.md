---

title: "Transfer Digital Credentials Securely - Requirements"
abbrev: "tigress-requirements"
category: info

docname: draft-tigress-requirements-latest
submissiontype: IETF
number:
date:
consensus: false
v: 3
area: "Applications and Real-Time"
workgroup: "Transfer dIGital cREdentialS Securely"
keyword:
 - tigress
 - requirements
venue:
  group: "Transfer dIGital cREdentialS Securely"
  type: "Working Group"
  mail: "tigress@ietf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/tigress/"
  github: "dimmyvi/tigress-requirements"
  latest: "https://datatracker.ietf.org/doc/draft-tigress-requirements/"

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
    email: castiz@apple.com
 -
    ins: A. Pelletier
    name: Alex Pelletier
    organization: Apple Inc
    email: a_pelletier@apple.com
 -
    ins: Y. Karandikar
    name: Yogesh Karandikar
    organization: Apple Inc
    email: ykarandikar@apple.com
 -
    ins: B Lassey
    name: Brad Lassey
    organization: Alphabet Inc
    email: lassey@google.com

normative:

informative:
  CCC-Digital-Key-30:
    author:
      org: Car Connectivity Consortium
    title: "Digital Key Release 3"
    date: 2022-07
    target: https://carconnectivity.org/download-digital-key-3-specification/


--- abstract


This document describes the use cases necessitating the secure transfer of digital credentials, residing in a digital wallet, between two devices and defines general assumptions, requirements and the scope for possible solutions to this problem.

--- middle

# Introduction

In this document we are identifying a problem of transferring digital credentials (e.g. a digital car key, a digital key to a hotel room) from one device (e.g. smartphone) to another. Today, there is no widely accepted way of transferring digital credentials securely between two devices from different manufacturers. 

A Working Group, called Tigress has been established to find a solution to the problem described above.
Within the WG an initial solution was presented (https://datatracker.ietf.org/doc/draft-art-tigress). The community decided to generalize the requirements to the solution and consider alternative solutions within the WG.

This document presents the general requirements to possible solutions and specifies certain privacy requirements in order to maintain a high level of user privacy.


# Conventions and Definitions

{::boilerplate bcp14-tagged}


General terms:

- Digital Credential - Cryptographic material and other data required to authenticate the user with an access point.
- Provisioning - A process of adding a new digital credential to the device.
- Provisioning Entity - an entity which facilitates Credential lifecycle on a device). Lifecycle may include provisioning of credential, credential termination, credential update.
- Provisioning information - data transferred from Sender to Receiver device that is both necessary and sufficient for the Receiver to get a new credential from Provisioning Entity.
- Sender (device) - a device initiating a transfer of Provisioning Information to a Receiver.
- Receiver (device) - a device that receives Provisioning Information and uses it to provision a new credential.
- Intermediary (server) - an optional server that provides a standardized and platform-independent way of transferring provisioning information between Sender and Receiver.
- Digital Wallet - A device, service, and/or software that holds credentials. It faciliates transactions either online or in-person via a technology like NFC. Digital Wallet's typically support payments, drivers licenses, loyalty cards, access credentials and more.


# Credential Transfer Scenarios

Credential types, provisioning entity policy can be proprietary or based published specifications. Based on credential type and Sender/Receiver - provisioning entity relationship, at least 3 types of transfer scenarios exist.

  1) Sender's crededential is copied in the provisioning process on the receiver. In this case two credentails on both devices are indistinguashable. This scenario is currently used by a very limited number of entities.
  2) Sender fetches a provisiong token from Provisiong entity and sends it to Receiver. Receiver can acquire new credential from Provisiong Entity by presenting the received provisiong token. In this case receiver's credential is different from sender's. Depending on the provisioning entity policy: Receiver credential may have same or less access rights and privileges; it may be revoked independently of the sender's credential; Sender and Receiver credentials can be linked to the same logical "user account" or differnet "user accounts".
  3) Sender is the provisioning authority. In this case, sender generates provisioning information and sends it to receiver. Receiver uses the information to generate the cryptographic material for the credential. Then reciver sends a signing request to sender. Sender's signature cpmpletes the flow and receiver gets a signed credential that will be functional. The two credentials are certainly different from each other. Similar to case 2, access rights and privileges could be same or different, "user account" could be same or different. 
  
Note that same device could play role of Sender in one situation and that of receiver in other. 

We need to accomodate all these types of credential transfers with a secure solution that preseves user privacy. Systems can be built based on a solution defined in Tigress WG to share various crdentials.


# Example Use Cases

- Ben owns a vehicle that supports digital car keys. Ben is out of town and would like to let Amit borrow the car for the weekend. 

- Bob booked a room at a hotel for the weekend, but will be arriving late at night. Alice, his partner, comes to the hotel first, so Bob wants to share his digital room key with Alice.

- Anita has hired Jared to walk her dog. Anita would like to share hey apartment key with Jared so he can take the dog for a walk.

In all such cases, sender should be able to transfer the digital credential in a seamless manner. To the user it should feel to be equivalent of communication via instant message, email, etc. 



# Transfer mechanism

Credential transfer between sender and receiver can happen in a direct manner or via an intermediary server.


## Credential transfer without intermediary

~~~ plantuml-utxt
participant Sender as S
participant Receiver as R
S -> R: transfer provisioning information E2E
loop Provision credential
  R -> S: request additional provisioning information
  S -> R: deliver additional provisioning information
end
~~~

## Credential transfer with intermediary server

~~~ plantuml-utxt
participant Sender as S
participant Intermediary as I
participant Receiver as R

S -> I: upload provisioning information
S -> R: send invite
loop Provision credential
  R -> I: request additional provisioning information
  I -> S: optional forward request
  S -> I: additional provisiniong information 
  I -> R: deliver additional provisioning information
end
~~~


# Assumptions

- Security: Communication between sender or receiver and Provisioning Entity should be trusted.
- If an intermediary server is used during the credential transfer, the choice of intermediary shall be made by Sender. 
- Sender and Receiver shall both be able to stop the transfer before the credential is provisioned at the receiver.
- Any device with a digital credential implementation adherent to Tigress solution shall be able to receive shared provisioning information.
- In case where Sender is also the provisioning entity (e.g. Sharign of CarKey), multiple round trips may be necessary.
- Some credentials require special HW (TEE - Trusted Execution Environment or SE - Secure Element orsimilar modules) to host and operate credential. But the transport protocol defined by Tigress does not set such requirements for the process of digital credentail transfer.

# Requirements

- (Req-XPlatform) Solution shall support transfer of digital credential across any platforms (e.g. from Android to iOS, KaiOS, UbuntuTouch or postmarketOS).
- (Req-CredentialType) The solution shall support transfer of various digital credential types, based on symmetric and asymmetric cryptography, public and proprietary standards.
- (Req-Security) Solution should provide security of the provisioning data transferred (confidentiality, integrity and availability of provisioning information in transit).
- (Req-NonCorelation) Transport protocol used to transfer provisioning information ( e.g. secure E2E transfer protocol or intermediary server) shall prevent from correlating users between exchanges or create a social graph of users involved into transfer.
- (Req-NonIdentity) Intermediary server shall not be an arbiter of identity.
- (Req-Connectivity) Sender and Receiver shall be allowed to be online at different times. Sender and Receiver shall not need to be online at the same time. This requirement allows devices to connect to network to only exchange the portion of information required during the transfer, allowing them upload or download data in turns to network servers.
- (Req-RoundTrips) Solution shall allow for multiple data exchanges between sender and receiver devices in the process of credential transfer. This requirement shall align with (Req-Connectivity) above.
- (Req-Opaque) In the case when an intermediary server is used to facilitate the credential transfer, message content between sender and receiver must be opaque to an intermediary, intermediary server shall not be able to recognize the content of provisioning information or use it to provision digital credential on its own.


# Security Considerations

- Threat model for implementation that uses an intermediary server to facilitate the credential transfer should consider trusted relationship between a sender device and the intermediary server. Intermediary server shall be able to verify that the sender device is in good standing and content generated by the sender device can be trusted by the intermediary. The trust mechanism could be proprietary or publicly verifiable ( e.g. WebAuthN). This is important because intermediary server shall have no visibility to the content of the provisiong information sent through it (Req-Opaque).
- Threat model for implementation that uses an intermediary server to facilitate the credential transfer, should consider evaluation of the trustworthiness of the intermediary server by the receiver device based on agreed criteria.
- Tigress implementation shall avoid collecting user or device identities, as well as storing and using such identities for purpose other then the credential transfer itself.

# General considerations

- A single token of Provisioning Information shall be used for a single transfer of digital credential, shall not be redeemable for multiple additional transfer attempts. Sharing in a chain (when Receiver, after provisioning the credential to the device, becomes a new Sender using the same token of Provisioning Information) or sharing to multiple devices shall not be used with a single piece of Provisioning Information.
- Implementation should be able to quickly and efficiently transfer data between Sender and Receiver devices. Mechanisms such as Push Notifications or Webhooks shall be used instead of mailbox polling to catch data updates in order to save device battery and network bandwidth.
- An invitation for a shared credential transfer, sent to the Receiver device shall be a self-contained and self-sufficienct data (e.g. token, URL, or QR code) allowing a user of the Receiver device to start a process of transferring and adding a new credential. Such invitation should be allowed to be sent over any generic communication channel (e.g. sms, email, NFC).


TODO Security


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
