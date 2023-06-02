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

--- abstract


This document describes the use cases necessitating the secure transfer of Digital Credentials, residing in a Digital Wallet, between two devices and defines general assumptions, requirements and the scope for possible solutions to this problem.

--- middle

# Introduction

In this document we are identifying a problem of transferring Digital Credentials (e.g. a digital car key, a digital key to a hotel room or a digital key to a private house) from one device (e.g. smartphone) to another.
Today, there is no widely accepted way of transferring Digital Credentials securely between two Digital Wallets independent of hardware and software manufacturer. This document describes the problem space and the requirements for the solution the working group creates.

A Working Group, called Tigress has been established to find a solution to the problem described above.
Within the WG an initial solution was presented (https://datatracker.ietf.org/doc/draft-art-tigress). The community decided to generalize the requirements to the solution and consider alternative solutions within the WG.

This document presents the general requirements to possible solutions and  specifies certain privacy requirements in order to maintain a high level of user privacy.


# General Setting

When sharing digital secure credentials, there are several actors involved. This document will focus on sharing information between two Digital Wallets, directly or through an intermediary server.

A Digital Credential provides access to a property that is owned or rented by the user or operated by 3-rd party entities. The entity that provides the Digital Credential for consumption by a Digital Wallet is referred to as the Provisioning Entity.

For most credentials, the Provisioning Entity may need to have control over issuance and life time management of the Digital Credential - for example, hotel management allows guests to access rooms and amenities only for the duration of their booked stay.

A Digital Wallet is a combination of software and hardware in a smartphone device. There are two devices involved in credential transfer process - Sender and Receiver. The device that initiates transfer is termed as the Sender and the device that eventually consumes the transfer is termed as the Receiver. Same device can play different roles in 2 different transactions (Sender in one and Receiver in another).

The interface between the device and the Provisioning Entity can be proprietary or a part of published specifications. The Sender obtains Provisioning Information from the Provisioning Entity, then shares it to the Receiver using a solution defined in Tigress WG. The Receiver then takes that Provisioning Information and redeems the credential from the Provisioning Entity.

For some credential types the Provisioning Entity who issues new credential is actually the Sender itself(e.g. Digital Car key). In that scenario, the Receiver will generate new key material based on the Provisioning Information, then get it signed by the Sender. That requires one or more round-trips between Receiver and Sender over Tigress.


# Conventions and Definitions

{::boilerplate bcp14-tagged}


General terms:

- Digital Credential - Cryptographic material and other data used to authorize User with an access point. The cryptographic material can also be used for mutual authentication between user device and access point.
- Provisioning - A process of adding a new Digital Credential to the device.
- Provisioning Entity - an entity which facilitates Digital Credential lifecycle on a device. Lifecycle may include Provisioning, termination, credential update.
- Provisioning Information - data transferred from Sender to Receiver device that is both necessary and sufficient for the Receiver to request a new credential from Provisioning Entity.
- Sender (device) - a device initiating a transfer of Provisioning Information to a Receiver that can provision this credential.
- Receiver (device) - a device that receives Provisioning Information and uses it to provision a new credential.
- Intermediary (server) - an optional intermediary server that provides a standardized and platform-independent way of transferring Provisioning Information between Sender and Receiver, acting as a temporary store and forward service.
- Digital Wallet - A device, service, and/or software that facilitates transactions either online or in-person via a technology like NFC. Digital Wallet's typically support payments, drivers licenses, loyalty cards, access credentials and more.


# Use Cases

- Ben owns a vehicle that supports digital car keys. Ben is out of town and realized that his car needs to be moved for street cleaning day. He asks his neighbor Amit to help and shares the car key when Amit agrees.

- Bob booked a room at a hotel for the weekend, but will be arriving late at night. Alice, his partner, comes to the hotel first, so Bob wants to share his digital room key with Alice.

- Bakari has hired Dorian to walk his dog for the next few days. Bakari shares the key to his apartment with Dorian so he can take the dog out.

In all such cases, Sender should be able to transfer the Digital Credential in a seamless manner. Sharing of credential should feel equivalent to regular communication via instant messaging, email etc.

# Relationships

## Credential transfer with Intermediary server

~~~ plantuml-utxt
participant Sender as S
participant Intermediary as I
participant Receiver as R

S -> I: upload Provisioning Information
S -> R: send invite
loop Provision credential
  R -> I: request Provisioning Information
  I -> R: deliver Provisioning Information
    opt Additional Data
      R -> I: additional data request
      I -> S: Forward request
      S -> I: additional data response
      I -> R: forward response
    end
end
~~~

## Credential transfer without Intermediary

~~~ plantuml-utxt
participant Sender as S
participant Receiver as R
S -> R: transfer Provisioning Information E2E
loop Provision credential
  R -> S: request Provisioning Information
  S -> R: deliver Provisioning Information
    opt Additional Data
      R -> S : Additional Data Request
      S -> R : Additional Data Response
    end
end
~~~

# Assumptions

- Based on credential type and Sender/Receiver - Provisioning Entity relationship, at least 3 types of transfer scenarios exist.
    1) Sender's credential is copied in the Provisioning process on the Receiver. In this case two credentials on both devices are indistinguishable. This scenario is currently used by a very limited number of entities.
    2) Sender fetches a provisioning token from Provisioning entity and sends it to Receiver. Receiver can acquire new credential from Provisioning Entity by presenting the received provisioning token. In this case Receiver's credential is different from Sender's. Depending on the Provisioning Entity policy: Receiver credential may have same or less access rights and privileges; it may be revoked independently of the Sender's credential; Sender and Receiver credentials can be linked to the same logical "user account" or different "user accounts".
    3) Sender is the Provisioning Entity. In this case, Sender generates Provisioning Information and sends it to Receiver. Receiver uses the information to generate the cryptographic material for the credential. Then Receiver sends a signing request to Sender. Sender's signature completes the flow and Receiver gets a signed credential that will be functional. The two credentials are certainly different from each other. Similar to case 2, access rights and privileges could be same or different, "user account" could be same or different.

- Security: Communication between Sender or Receiver devices and Provisioning Entity should be trusted. If the new credential's key material is generated by Provisioning Entity, the channel between the device and Provisioning Entity shall be secure and trusted by both parties.

- In case of an intermediary server, used during the credential transfer from Sender to Receiver, the choice of Intermediary shall be defined by the application initiating the credential transfer. Digital wallet or another application that manages credentials on Sender shall make the decision regarding the channel to be used to sent the Provisioning Information.

- Sender and Receiver shall both be able to manage the shared credential at any point in transfer or lifecycle: a) the process of credential transfer can be stopped at any time before the credential is provisioned to the receiver device by either Sender or receiver device (e.g. making a call to intermediate server to delete a temporary mailbox); b) or after credential has been provisioned - by either "manage credential" call issued from sender device to Provisioning Entity (or from Provisioning Entity initiating "manage credential" API).

- Any device OEM with a Digital Credential implementation adherent to Tigress solution shall be able to receive shared Provisioning Information, whether or not they can originate Provisioning Information themselves. We define the Digital Credential transfer as platform-independent; therefore, if the receiver device can recognize the data format of the received Provisioning Information, it should be able to provision the new credential to the Digital Wallet.

- Provisioning new credential on the Receiver may require multiple round trips. In case the Sender is the Provisioning Entity for a certain type of credentials (e.g. digital car key), both Sender and Receiver are involved in generating new credential.

- Some credentials require special HW (TEE - Trusted Execution Environment or SE - Secure Element or similar modules) to host and operate credential. But the transport protocol defined by Tigress does not set such requirements for the process of Digital Credential transfer.


# Requirements

- (Req-XPlatform) Solution shall support transfer of Digital Credential across any platforms (e.g. from Android to iOS, KaiOS, UbuntuTouch or postmarketOS).
- (Req-CredentialType) The solution shall support transfer of various Digital Credential types, based on symmetric and asymmetric cryptography, public and proprietary standards.
- (Req-Security) Solution should provide security of the provisioning data transferred (confidentiality, integrity and availability of Provisioning Information in transit).
- (Req-NonCorrelation) Transport protocol used to transfer Provisioning Information ( e.g. secure E2E transfer protocol or intermediary server) shall prevent from correlating users between exchanges or create a social graph of users involved into transfer.
- (Req-NonIdentity) Intermediary server shall not be an arbiter of identity.
- (Req-Connectivity) Sender and Receiver shall be allowed to be online at different times. Sender and Receiver shall not need to be online at the same time. This requirement allows devices to connect to network to only exchange the portion of information required during the transfer, allowing them upload or download data in turns to network servers.
- (Req-RoundTrips) Solution shall allow for multiple data exchanges between Sender and Receiver in the process of credential transfer. This requirement shall align with (Req-Connectivity) above.
- (Req-ConnectionIntegrity) When Provisioning process requires multiple data exchanges (as described in Req-RoundTrips), no third party shall be allowed to interfere between Sender and Receiver. The solution shall provide integrity to the connection between Sender and Receiver for the duration of the transfer. The transfer ends when either Sender or Receiver ends it.
- (Req-Opaque) In the case when an intermediary server is used to facilitate the credential transfer, message content between Sender and Receiver must be opaque to an intermediary, intermediary server shall not be able to recognize the content of Provisioning Information or use it to provision Digital Credential on its own.
- (Req-Retrievals) : Sender should be able to specify the max number of unique Receivers that can receive Provisioning Information from the Tigress solution.

# Security and Privacy Considerations

- Threat model for implementation that uses an intermediary server to facilitate the credential transfer should consider trusted relationship between a sender device and the intermediary server. Intermediary server shall be able to verify that the sender device is in good standing and content generated by the sender device can be trusted by the Intermediary. The trust mechanism could be proprietary or publicly verifiable ( e.g. WebAuthN). This is important because intermediary server shall have no visibility to the content of the Provisioning Information sent through it (Req-Opaque).
- Threat model for implementation that uses an intermediary server to facilitate the credential transfer, should consider evaluation of the trustworthiness of the intermediary server by the receiver device based on agreed criteria.
- Tigress implementation shall avoid collecting user or device identities, as well as storing and using such identities for purpose other then the credential transfer itself.


# General considerations

- A single token of Provisioning Information shall be used for a single transfer of Digital Credential. It shall not be redeemable for multiple additional transfer attempts. Receiver of a Digital Credential, can initiate a transfer of the same credential  after Provisioning. But for that, the Receiver shall assume the Sender role and get new Provisioning Information to share.
- Implementation should be able to quickly and efficiently transfer data between Sender and Receiver devices. Mechanisms such as Push Notifications or Webhooks shall be used instead of mailbox polling to catch data updates in order to save device battery and network bandwidth.
- An invitation for a shared credential transfer, sent to the Receiver device shall be a self-contained and self-sufficient data (e.g. token, URL, or QR code) allowing a user of the Receiver device to start a process of transferring and adding a new credential. Such invitation should be allowed to be sent over any generic communication channel (e.g. sms, email, NFC).
- When both Sender and Receiver are online at the same time they should be able to quickly and efficiently transfer data. Implementor should consider performance factors such as battery power and network performance when implementing the solution for the data exchange. Notification-based approach is preferable comparing to polling-based.```
l


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
