---
stand_alone: true
ipr: trust200902
docname: draft-moskowitz-drip-crowd-sourced-rid-04
cat: std
obsoletes: ''
updates: ''
submissiontype: IETF
lang: en
pi:
  toc: true
  symrefs: true
  sortrefs: true
  compact: yes
  subcompact: no
  iprnotified: no
  strict: no
title: Crowd Sourced Remote ID
abbrev: CS-RID
area: Internet
wg: DRIP
kw: RFC
date: 2020
author:
- ins: R. Moskowitz
  name: Robert Moskowitz
  org: HTT Consulting
  street: ''
  city: Oak Park
  region: MI
  code: '48237'
  country: USA
  email: rgm@labs.htt-consult.com
- ins: S. Card
  name: Stuart W. Card
  org: AX Enterprize
  street: 4947 Commercial Drive
  city: Yorkville
  region: NY
  code: '13495'
  country: USA
  email: stu.card@axenterprize.com
- ins: A. Wiethuechter
  name: Adam Wiethuechter
  org: AX Enterprize
  street: 4947 Commercial Drive
  city: Yorkville
  region: NY
  code: '13495'
  country: USA
  email: adam.wiethuechter@axenterprize.com
- ins: S. Zhao
  name: Shuai Zhao
  org: Tencent
  street: 2747 Park Blvd
  city: Palo Alto
  region: CA
  code: '94306'
  country: USA
  email: shuai.zhao@ieee.org
normative:
  RFC8610:
informative:
  F3411-19:
    target: http://www.astm.org/cgi-bin/resolver.cgi?F3411
    title: Standard Specification for Remote ID and Tracking
    author:
    - org: ASTM International
    date: 2020-02
  FAA-NPRM:
    target: https://www.regulations.gov/docket?D=FAA-2019-1100
    title: FAA Remote ID Notice of Proposed Rule Making
    author:
    - org: Federal (US) Aviation Authority
    date: 2019-12
  tmrid-auth: I-D.wiethuechter-tmrid-auth
  hierarchical-hit: I-D.moskowitz-hip-hierarchical-hit
  hhit-registries: I-D.moskowitz-hip-hhit-registries
  RFC7252:
  RFC5238:
  RFC4303:
  RFC7401:
  RFC7049:
  RFC8152:
  RFC8032:

--- abstract


This document describes using the ASTM Broadcast Remote ID (B-RID)
specification in a "crowd sourced" smart phone environment to
provide much of the FAA mandated Network Remote ID (N-RID)
functionality.  This crowd sourced B-RID data will use
multilateration to add a level of reliability in the location data
on the Unmanned Aircraft (UA).  The crowd sourced environment will
also provide a monitoring coverage map to authorized observers.

--- middle

# Introduction

This document defines a mechanism to capture the ASTM Broadcast
Remote ID messages (B-RID) {{F3411-19}} on any
Internet connected device that receives
them and can forward them to the SDSP(s) responsible for the
geographic area the UA and receivers are in.  This will create a
ecosystem that will meet most if not all data collection
requirements that CAAs are placing on Network Remote ID (N-RID).

These Internet connected devices are herein called "Finders", as
they find UAs by listening for B-RID messages.  The Finders are
B-RID forwarding proxies.  Their potentially limited spacial view
of RID messages could result in bad decisions on what messages to
send to the SDSP and which to drop.  The SDSP will make any
filtering decisions in what it forwards to the UTM(s).

Finders can be smartphones, tablets, connected cars, or any
computing platform with Internet connectivity that can meet the
requirements defined in this document.  It is not expected, nor
necessary, that Finders have any information about a UAS beyond the
content in the B-RID messages.

Finders MAY only need a loose association with the SDSP(s).  They
may only have the SDSP's Public Key and FQDN.  It would use these,
along with the Finder's Public Key to use ECIES, or other security
methods, to send the messages in a secure manner to the SDSP.  The
SDSP MAY require a stronger relationship to the Finders.  This may
range from the Finder's Public Key being registered to the SDSP
with other information so that the SDSP has some level of trust in
the Finders to requiring transmissions be sent over long-lived
transport connections like ESP or DTLS.

This document has minimal information about the actions of SDSPs.
In general the SDSP is out of scope of this document. That said,
the SDSPs should not simply proxy B-RID messages to the UTM(s).
They should perform some minimal level of filtering and content
checking before forwarding those messages that pass these tests in
a secure manner to the UTM(s).

The SDSPs are also capable of maintaining a monitoring map, based
on location of active Finders.  UTMs may use this information to
notify authorized observers of where this is and there is not
monitoring coverage.  They may also use this information of where
to place pro-active monitoring coverage.

An SDSP SHOULD only forward Authenticated B-RID messages like those
defined in {{tmrid-auth}} to the UTM(s).  Further, the SDSP SHOULD
validate the Remote ID (RID) and the Authentication signature
before forwarding anything from the UA.

When 3 or more Finders are reporting to an SDSP on a specific UA,
the SDSP is in a unique position to perform multilateration on
these messages and compute the Finder's view of the UA location to
compare with the UA Location/Vector messages.  This check against
the UA's location claims is both a validation on the UA's
reliability as well as the trustworthiness of the Finders.  Other
than providing data to allow for multilateration, this SDSP feature
is out of scope of this document.

## Draft Status

This draft is still incomplete.  New features are being added as
capabilities are researched.  The actual message formats also still
need work.



# Terms and Definitions {#terms}

## Requirements Terminology

{::boilerplate bcp14}


## Definitions

{: vspace='0'}

B-RID
: Broadcast Remote ID. A method of sending RID messages as
  1-way transmissions from the UA to any Observers within
  radio range.

CAA
: Civil Aeronautics Administration. An example is the Federal
  Aviation Administration (FAA) in the United States of
  America.

DAA
: Detect and Avoid. The process of a UA detecting obstacles,
  like other UAs and taking the necessary evasive action.

ECIES
: Elliptic Curve Integrated Encryption Scheme.  A hybrid
  encryption scheme which provides semantic security against
  an adversary who is allowed to use chosen-plaintext and
  chosen-ciphertext attacks.

GCS
: Ground Control Station. The part of the UAS that the remote
  pilot uses to exercise C2 over the UA, whether by remotely
  exercising UA flight controls to fly the UA, by setting GPS
  waypoints, or otherwise directing its flight.

Finder
: In Internet connected device that can receive B-RID
  messages and forward them to a UTM.

Observer
: Referred to in other UAS documents as a "user", but there
  are also other classes of RID users, so we prefer
  "observer" to denote an individual who has observed an UA
  and wishes to know something about it, starting with its
  RID.

Multilateration
: Multilateration (more completely, pseudo range
  multilateration) is a navigation and surveillance technique
  based on measurement of the times of arrival (TOAs) of
  energy waves (radio, acoustic, seismic, etc.) having a
  known propagation speed.

NETSP
: Network RID Service Provider. USS receiving Network RID
  messages from UAS (UA or GCS), storing for a short
  specified time, making available to NETDP.

NETDP
: Network RID Display Provider. Entity (might be USS)
  aggregating data from multiple NETSPs to answer query from
  observer (or other party) desiring Situational Awareness of
  UAS operating in a specific airspace volume.

N-RID
: Network Remote ID. A method of sending RID messages via the
  Internet connection of the UAS directly to the UTM.

RID
: Remote ID. A unique identifier found on all UA to be used
  in communication and in regulation of UA operation.

SDSP
: Supplemental Data Service Provider. Entity providing
  information that is allowed, but not required to be present
  in the UTM system.

UA
: Unmanned Aircraft. In this document UA's are typically
  though of as drones of commercial or military variety. This
  is a very strict definition which can be relaxed to include
  any and all aircraft that are unmanned.

UAS
: Unmanned Aircraft System. Composed of Unmanned Aircraft and
  all required on-board subsystems, payload, control station,
  other required off-board subsystems, any required launch
  and recovery equipment, all required crew members, and C2
  links between UA and the control station.

UTM
: UAS Traffic Management. A "traffic management" ecosystem
  for uncontrolled operations that is separate from, but
  complementary to, the FAA's Air Traffic Management (ATM)
  system.

USS
: UAS Service Supplier. Provide UTM services to support the
  UAS community, to connect Operators and other entities to
  enable information flow across the USS network, and to
  promote shared situational awareness among UTM
  participants. (From FAA UTM ConOps V1, May 2018).



# Problem Space {#prob-space}

## Meeting the needs of Network ID

The Federal (US) Aviation Authority (FAA), in the December 31, 2019
Remote ID Notice of Proposed Rulemaking {{FAA-NPRM}}, is requiring
"Standard" and "Limited" Remote
ID.  Standard is when the UAS provides both Network and Broadcast
RID.  Limited is when the UAS provides only Network RID.  The FAA
has dropped their previous position on allowing for only Broadcast
RID.  We can guess as to their reasons; they are not spelled out in
the NPRM.  It may be that just B-RID does not meet the FAA's
statutory UA tracking responsibility.

The UAS vendors have commented that N-RID places considerable
demands on currently used UAS.  For some UAS like RC planes,
meaningful N-RID (via the Pilot's smartphone) are of limited value.
A mechanism that can augment B-RID to provide N-RID would help all
members of the UAS environment to provide safe operation and allow
for new applications.


## Advantages of Broadcast Remote ID

B-RID has its advantages over N-RID.

* B-RID can more readily be implemented directly in the UA.
  N-RID will more frequently be provided by the GCS or a
  pilot's Internet connected device.
  * If Command and Control (C2) is bi-directional over
    a direct radio connection, B-RID could be a
    straight-forward addition.

  * Small IoT devices can be mounted on UA to provide B-RID.


* B-RID can also be used by the UA to assist in Detect and
  Avoid (DAA).

* B-RID is available to observers even in situations with no
  Internet like natural disaster situations.


## Trustworthiness of Proxied Data

When a proxy is introduced in any communication protocol, there is
a risk of corrupted data and DOS attacks.

The Finders, in their role as proxies for B-RID, are authenticated
to the SDSP (see {{Finder_Sec}}). The
SDSP can compare the information from multiple Finders to isolate a
Finder sending fraudulent information.  SDSPs can additionally
verify authenticated messages that follow {{tmrid-auth}}.

The SPDP can manage the number of Finders in an area (see
{{Finder_Manage}}) to limit DOS attacks
from a group of clustered Finders.


## Defense against fraudulent RID Messages

The strongest defense against fraudulent RID messages is to focus
on {{tmrid-auth}} conforming messages.  Unless this behavior is
mandated, SPDPs will have to use assorted algorithms to isolate
messages of questionable content.



# The Finder - SDSP Security Relationship {#Finder_Sec}

The SDSP(s) and Finders SHOULD use [EDDSA](#RFC8032) keys as their
trusted Identities.
The public keys SHOULD be registered Hierarchical HITS,
{{hierarchical-hit}} and {{hhit-registries}}.

The SDSP uses some process (out of scope here) to register the
Finders and their EDDSA Public Key.  During this registration, the
Finder gets the SDSP's EDDSA Public Key.  These Public Keys allow
for the following options for authenticated messaging from the
Finder to the SDSP.

1. ECIES can be used with a unique nonce to authenticate each
  message sent from a Finder to the SDSP.

1. ECIES can be used at the start of some period (e.g. day) to
  establish a shared secret that is then used to authenticate
  each message sent from a Finder to the SDSP sent during
  that period.

1.  [HIPv2](#RFC7401) can be used to
  establish a session secret that is then used with [ESP](#RFC4303) to authenticate each message
  sent from a Finder to the SDSP.

1.  [DTLS](#RFC5238) can be used to establish
  a secure connection that is then used to authenticate each
  message sent from a Finder to the SDSP.

## The Finder Map {#Finder_Map}

The Finders are regularly providing their SDSP with their location.
This is through the B-RID Proxy Messages and Finder Location Update
Messages.  With this information, the SDSP can maintain a
monitoring map.  That is a map of where there Finder coverage.


## Managing Finders {#Finder_Manage}

Finder density will vary over time and space.  For example,
sidewalks outside an urban train station can be packed with
pedestrians at rush hour, either coming or going to their commute
trains.  An SDSP may want to proactively limit the number of active
Finders in such situations.

Using the Finder mapping feature, the SDSP can instruct Finders to
NOT proxy B-RID messages.  These Finders will continue to report
their location and through that reporting, the SDSP can instruct
them to again take on the proxying role.  For example a Finder
moving slowly along with dozens of other slow-moving Finders may be
instructed to suspend proxying.  Whereas a fast-moving Finder at
the same location (perhaps a connected car or a pedestrian on a
bus) would not be asked to suspend proxying as it will soon be out
of the congested area.



# The CS-RID Messages {#CSRID_messages}

The CS-RID messages between the Finders and the SDSPs primarily
support the proxy role of the Finders in forwarding the B-RID
messages.  There are also Finder registration and status messages.

CS-RID information is represented in CBOR {{RFC7049}}.  COSE {{RFC8152}} may be used for CS-RID MAC and
COAP {{RFC7252}} for the CS-RID protocol.

The following is a general representation of the content in the
CS-RID messages.

~~~~
         (   CS-RID MESSAGE TYPE,
             CS-RID MESSAGE CONTENT,
             CS-RID MAC)

~~~~

The CS-RID MESSAGE CONTENT varies by MESSAGE TYPE.

## CS-RID MESSAGE TYPE {#CS-RID_MESSAGE_TYPE}

The CS-RID MESSAGE TYPE is:

~~~~
Number   CS-RID Message Type
------   -----------------
0        Reserved
1        B-RID Forwarding
2        Finder Registration
3        SDSP Response
4        Finder Location

~~~~


## The CS-RID B-RID Proxy Message {#CSRID_proxy}

The Finders add their own information to the B-RID messages,
permitting the SDSP(s) to gain additional knowledge about the
UA(s).  The RID information is the B-RID message content plus the
MAC address.  The MAC address is critical, as it is the only
field that links a UA's B-RID messages together.  Only the ASTM
Basic ID Message and possibly the Authentication Message contain
the UAS ID field.

The Finders add an SDSP assigned ID, a 64 bit timestamp, GPS
information, and type of B-RID media to the B-RID message.  Both
the timestamp and GPS information are for when the B-RID
message(s) were received, not forwarded to the SDSP.  All this
content is MACed using a key shared between the Finder and SDSP.

The following is a representation of the content in the CS-RID
messages.

~~~~
         (   CS-RID MESSAGE TYPE,
             CS-RID ID,
             RECEIVE TIMESTAMP,
             RECEIVE GPS,
             RECEIVE RADIO TYPE,
             B-RID MAC ADDRESS,
             B-RID MESSAGE,
             CS-RID MAC)

~~~~



### CS-RID ID {#CS-RID_ID}

The CS-RID ID is the ID recognized by the SDSP.  This may be an
HHIT [Hierarchical HITs](#hierarchical-hit), or any ID used by the SDSP.



## CS-RID Finder Registration {#CS-RID_Reg}

The CS-RID Finder MAY use [HIPv2](#RFC7401) with
the SDSP to establish a Security Association and a shared secret to
use for the CS-RID MAC generation.  In this approach, the HIPv2
mobility functionality and [ESP](#RFC4303) support are not used.

When HIPv2 is used as above, the Finder Registration is a SDSP
"wake up".  It is sent prior to the Finder sending any proxied
B-RID messages to ensure that the SDSP is able to receive and
process the messages.

In this usage, the CS-RID is the Finder HIT.  If the SDSP has lost
state with the Finder, it initiates the HIP exchange with the
Finder to reestablish HIP state and a new shared secret for the
CS-RID B-RID Proxy Messages.  In this case the Finder Registration
Message is:

~~~~
         (   CS-RID MESSAGE TYPE,
             CS-RID ID,
             CS-RID TIMESTAMP,
             CS-RID GPS,
             CS-RID MAC)

~~~~


## CS-RID SDSP Response {#SDSP_Reg_R}

The SDSP MAY respond to any Finder messages to instruct the Finder on its
behavior.

~~~~
         (   CS-RID MESSAGE TYPE,
             SDSP ID,
             CS-RID ID,
             CS-RID PROXY STATUS,
             CS-RID UPDATE INTERVAL,
             CS-RID MAC)

~~~~

The Proxy Status instructs the Finder if it should actively proxy
B-RID messages, or suspend proxying and only report its location.

The Update Interval is the frequency that the Finder SHOULD notify
the SDSP of its current location using the Location Update message.


## CS-RID Location Update {#CS-RID_Upd}

The Finder SHOULD provide regular location updates to the SDSP. The
interval is based on the Update Interval from {{SDSP_Reg_R}} plus a random slew less than
1 second.  The Location Update message is only sent when no other
CS-RID messages, containing the Finder's GPS location, have been
sent since the Update Interval.

If the Finder has not recieved a SDSP Registration Response, a
default of 5 minutes is used for the Update Interval.

~~~~
         (   CS-RID MESSAGE TYPE,
             CS-RID ID,
             CS-RID TIMESTAMP,
             CS-RID GPS,
             CS-RID MAC)

~~~~



# IANA Considerations {#IANA}

TBD


# Security Considerations

TBD

## Privacy Concerns

TBD



--- back

# Using LIDAR for UA location {#LIDAR}

If the Finder has LIDAR or similar detection equipment (e.g. on a
connected car) that has full sky coverage, the Finder can use this
equipment to locate UAs in its airspace.  The Finder would then be
able to detect non-participating UAs.  A non-participating UA is
one that the Finder can "see" with the LIDAR, but not "hear" any
B-RID messages.

These Finders would then take the LIDAR data, construct appropriate
B-RID messages, and forward them to the SPDP as any real B-RID
messages.  There is an open issue as what to use for the actual
RemoteID and MAC address.

The SDSP would do the work of linking information on a
non-participating UA that it has received from multiple Finders
with LIDAR detection.  In doing so, it would have to select a
RemoteID to use.

A seemingly non-participating UA may actually be a UA that is
beyond range for its B-RID but in the LIDAR range.

This would provide valuable information to SDSPs to forward to UTMs
on potential at-risk situations.

At this time, research on LIDAR and other detection technology is
needed.  there are full-sky LIDAR for automotive use with ranges
varying from 20M to 250M.  Would more than UA location information
be available?  What information can be sent in a CS-RID message for
such "unmarked" UAs?


# CDDL (Normative)  {#cddl}

The CDDL {{RFC8610}} specification for CS-RID message is listed in this appendix.

The CS-RID MESSAGE TYPE is:

~~~~
; CDDL specification for Crowd source RID
; It specifies a collection of CS message types
;

;
; The CSRID overall data structure
;
CSRID_Object = {
    application-context,
    ? message_info          : message
    ? proxy_message-type    : proxy_message,
    ? finder_registration   : finder_registration,
    ? sdsp_response         : sdsp_response,
    ? location_update       : location_update,
}

;
; Application context: general information about CSRID message (optional)
;
application-context = (
    "application"   => "DRIP-CSRID",
    ? "version"     => uint .size(1..2),
)

;
; CSRID message general information
;
message = {
    message_types,      ; message type (required)
    message_content,    ; message content (required)
    mac_address,        ; MAC address (required)
}

;
; Broadcast RID proxy message
;
proxy_message = {
    message_types,          ; message type (required)
    id,                     ; rid   (required)
    timestamp,              ; received timestamp (required)
    gps,                    ; received GPS coordination (required)
    radio_type,             ; received radio types (required)
    broadcast_mac_address,  ; broadcast MAC address (required)
    broadcast_message,      ; broadcast message (required)
    mac_address,            ; MAC address (required)
}

; Finder Registration
;

finder_registration = {
    message_types,  ; message type (required)
    id,             ; rid (required)
    timestamp,      ; timestamp (required)
    gps,            ; GPS coordination (required)
    mac_address,    ; MAC address (required)
}

; SDSP Response
;
sdsp_response = {
    message_types,    ; message type (required)
    sdsp_id,         ; SDSP ID (required)
    id,              ; rid (required)
    proxy_status,    ; proxy status (required)
    update_interval, ; update internval (required)
    mac_address,     ; MAC address
}

; Location update
;
location_update = {
    message_types,   ; message types (required)
    id,              ; rid (required)
    timestamp,       ; timestamp (required)
    gps,             ; GPS coordination (required)
    mac_address,     ; MAC address (required)
}

;
; Common rule definition
;

; ID definition, text string (required)
id = ( id : tstr) ;

; predefined message types
message_types = (
    "message_type" => message_type,
)
message_type = &message_type_all
message_type_all = (
    Reserved            : 0,
    BRD                 : 1,
    Finder-Registration : 2,
    SDSP-Response       : 3,
    Finder-Location     : 4,
)

; timestamp
timestamp = (timestamp : tdate)

; GPS coordination (required)
gps = (GPS : [ coordination])
coordination = (
    lat: float,
    long: float
)

; Radio types, choose from one of radio_types (required)
radio_type = ( radio_type : radio_type_choice )
radio_type_choice = &(
    EFL : 0,
    VLF : 1,
    LF  : 2,
    MF  : 3,
    HF  : 4,
    HF  : 5,
    VHF : 6,
    UHF : 7,
    SHF : 8,
    EHF : 9,
)

; Broadcast MAC address (required)
broadcast_mac_address = (
    broadcast_MAC_address :  #6.37(bstr)
)

; Broadcst message content (required)
broadcast_message = ( broadcast_message : tstr )

; MAC address
mac_address = ( MAC_address: #6.37(bstr) )

; Message content
message_content = ( message_content: tstr)

; SDDP ID
sdsp_id = (sdsp_id : tstr)

; Proxy status
proxy_status = (status : proxy_status_choice)
proxy_status_choice = &(
    0: "forward",
    1: "reverse",
    2: "bi",
)

; Update Interval
update_interval = (update_interval : uint)



~~~~


# Acknowledgments
{: numbered="no"}

The Crowd Sourcing idea in this document came from the Apple "Find
My Device" presentation at the International Association for
Cryptographic Research's Real World Crypto 2020 conference.

