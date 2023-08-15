---
title: "Transferring Digital Credentials with HTTP"
abbrev: "TDCH"
category: info

docname: draft-rescorla-tigress-http-latest
submissiontype: IETF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
area: "Applications and Real-Time"
workgroup: "Transfer dIGital cREdentialS Securely"
keyword:
 - next generation
 - unicorn
 - sparkling distributed ledger
venue:
  group: "Transfer dIGital cREdentialS Securely"
  type: "Working Group"
  mail: "tigress@ietf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/tigress/"
  github: "ekr/draft-rescorla-tigress-http"
  latest: "https://ekr.github.io/draft-rescorla-tigress-http/draft-rescorla-tigress-http.html"

author:
 -
    fullname: Eric Rescorla
    organization: Windy Hill Systems, LLC
    email: "ekr@rtfm.com"

normative:

informative:


--- abstract

There are many systems in which people use "digital credentials" to
control real-world systems, such as digital car keys, digital hotel
room keys, etc. In these settings, it is common for one person to want
to transfer their credentials to another, e.g., to share your hotel
key. It is desirable to be able to initiate this transfer with a
single message (e.g., SMS) which kicks off the transfer on the
receiver side. However, in many cases the credential transfer itself
cannot be completed over these channels, e.g., because it is too large
or because it requires multiple round trips. However, the endpoints
cannot speak directly to each other and may not even be online at the
same time. This draft defines a mechanism for providing an appropriate
asynchronous channel using HTTP as a dropbox.

--- middle

# Introduction

There are many systems in which people use "digital credentials" to
control real-world systems, such as digital car keys, digital hotel
room keys, etc. Generally these are proprietary system-specific
credentials are embedded in and used by a (potentially proprietary)
mobile app.  In these settings, it is common for one person to want to
transfer their credentials to another, e.g., to share your hotel key
with a family member.

Although the credentials and transfer mechanisms are often proprietary
they share a common workflow in which:

1. The Sender initiates the transfer from their app and sends
   an invitation message over a preexisting channel such as
   SMS or e-mail.

1. Bob receives the invitation message from Alice and hands
   it to his app (ideally this would happen automatically,
   e.g., by some URL handler).

1. Bob uses the invittion message to contact Alice to complete
   the transfer. This may require multiple round trips between
   Alice and Bob. In addition, Alice or Bob may need to contact
   some external server, but this is out of scope for this
   protocol.

The preexisting channel may not be suitable for completing the
transfer, for instance because it has insufficient bandwidth.  or
because it requires manual intervention by the users. In addition,
the participants may not be online simultaneously, so a
"store-and-forward" channel is required. {{?I-D.ietf-tigress-requirements}}
describes the requirements in more detail. This document specifies
how to build such a channel using a standard HTTP {{!RFC9110}} server.


# Conventions and Definitions

{::boilerplate bcp14-tagged}


# Overview of Operation

{{fig-overview}} provides a broad overview of the message flow:

~~~ aasvg
Alice                     HTTP Server                    Bob

Initiating (R) -------------------------------------------->
PUT <L0> MSG0 ---------------->
                              <-------------------- GET <L0>
                              <----------------- DELETE <L0>
                              <--------------- PUT <L1> MSG1
GET <L1> --------------------->
<------------------------- MSG1
DELETE <MSG1> ---------------->
                             ...
~~~
{: #fig-overview title="Overview of Operation"}

In order to initiate the transfer, Alice generates a random secret
value R. She then does the following:

1. Sends R and the address of the HTTP server to Bob over the
   preexisting channel.

1. Generates the first protocol message MSG0 and stores it in
   a location on the HTTP server (L0) pseudorandomly generated from
   R.

When Bob receives the initiating message, he uses R to determine L0,
retrieves it from the server, and then deletes it. In order to send a
message (MSG1) to Alice, Bob stores it at a new pseudorandom location
L1 (again, based on R). Alice retrieves it and then deletes it. Any
further message exchanges proceed in the same fashion.


# Architectural Model

The overall system has the following architecture:

~~~ aasvg
+-----------------------------------------------+
|   Credential Exchange Protocol (proprietary)  |
+-----------------------------------------------+
|    Protected Message Format (Section TODO)    |
+-----------------------------------------------+
|           HTTP Binding (Section TODO)         |
+-----------------------------------------------+
~~~

The lowest level of operation is a binding to HTTP specifying
how to use an HTTP server as a store-and-forward channel,
specified in {{http-binding}}. That channel is then used to carry
encrypted messages in the format defined in {{message-format}}.
Those messages contain an opaque payload that is used by
the relevant proprietary credential exchange protocol.


# HTTP Binding

# Message Format




# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
