---
title: "QUIC and HTTP/3 Datagram event definitions for qlog"
abbrev: "qlog Datagram extension"
category: info

docname: draft-marx-quic-qlog-datagram-latest
submissiontype: IETF  # also: "independent", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
area: "Transport"
workgroup: "QUIC"
keyword:
 - qlog
 - datagram
venue:
  group: "QUIC"
  type: "Working Group"
  mail: "quic@ietf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/quic/"
  github: "rmarx/draft-marx-quic-qlog-datagram"
  latest: "https://rmarx.github.io/draft-marx-quic-qlog-datagram/draft-marx-quic-qlog-datagram.html"

author:
 -
    fullname: Robin Marx
    organization: Akamai
    email: rmarx@akamai.com

normative:

  QLOG-MAIN:
    I-D.ietf-quic-qlog-main-schema

  QLOG-QUIC:
    I-D.ietf-quic-qlog-quic-events

  QLOG-HTTP3:
    I-D.ietf-quic-qlog-h3-events

informative:

--- abstract

This document describes qlog data type definitions for both the QUIC Datagram
Frame defined in {{!RFC9221}} and the HTTP/3 Datagram Frame defined in
{{!RFC9297}}. These data types are intended for use within event definitions
defined in {{QLOG-QUIC}} and {{QLOG-HTTP3}}.

--- middle

# Introduction

This document describes qlog data type definitions for both the QUIC Datagram
Frame defined in {{!RFC9221}} and the HTTP/3 Datagram Frame defined in
{{!RFC9297}}. These data types are intended for use within event definitions
defined in {{QLOG-QUIC}} and {{QLOG-HTTP3}}.

This document extends the `$QuicFrame` extension point defined in {{QLOG-QUIC}}
and the `$HTTPFrame` extension point defined in {{QLOG-HTTP3}}. It also defines
how to log a QUIC transport parameter for indicating QUIC datagram support, as
well as an HTTP/3 setting to indicate HTTP/3 datagram support.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

The event and data structure definitions in ths document are expressed
in the Concise Data Definition Language {{!CDDL=RFC8610}} and its
extensions described in {{QLOG-MAIN}}.

The following fields from {{QLOG-MAIN}} are imported and used: data and RawInfo.

# QUIC Datagrams

qlog support for QUIC Datagram frames consists of two aspects:

* support for logging the max_datagram_frame_size QUIC transport parameter
* support for logging the type, length and contents of the DATAGRAM frame itself

## max_datagram_frame_size transport parameter

Support for the QUIC Datagram extension defined in {{!RFC9221}} is signaled using the `max_datagram_frame_size` transport parameter, defined in {{Section 3 of !RFC9221}}.

If utilized, this transport parameter MUST be logged as a field of the
`transport:parameters_set` event defined in {{QLOG-QUIC}}, more specifically:

~~~ cddl
TransportParametersSet = {
    ? max_datagram_frame_size: uint64
    * text => any
}
~~~
{: #transport-parametersset-example title="TransportParametersSet extended with the max_datagram_frame_size parameter"}

## QUICDatagramFrame Definition

This section describes a qlog data type that can be used to log the contents of
the QUIC DATAGRAM frame defined in {{Section 4 of !RFC9221}}.

The `$QuicFrame` extension point defined in {{QLOG-QUIC}} is utilized to link
the QUICDatagramFrame type to the existing qlog data types.

~~~ cddl
QUICDatagramFrame = {
    frame_type: "datagram"
    ? length: uint64
    ? raw: RawInfo
}

$QuicFrame /= QUICDatagramFrame
~~~
{: #quicdatagramframe-def title="QUICDatagramFrame definition"}

# HTTP/3 Datagrams

qlog support for HTTP/3 Datagram frames consists of two aspects:

* support for logging the SETTINGS_H3_DATAGRAM HTTP/3 setting
* support for logging the type and contents of the HTTP/3 DATAGRAM frame itself

## SETTINGS_H3_DATAGRAM setting

Support for the HTTP/3 Datagram extension defined in {{!RFC9297}} is signaled using the `SETTINGS_H3_DATAGRAM` setting, defined in {{Section 2.1.1 of !RFC9297}}.

If utilized, this setting MUST be logged as a field of the `HTTPSetting` data
type defined in {{QLOG-HTTP3}}, more specifically:

~~~ cddl
HTTP3DatagramSetting = {
    name: "SETTINGS_H3_DATAGRAM"
    value: uint16
}
~~~
{: #http3datagramsetting-example title="HTTPSetting instance for SETTINGS_H3_DATAGRAM"}

## HTTPDatagramFrame Definition

This section describes a qlog data type that can be used to log the contents of
the HTTP/3 DATAGRAM frame defined in {{Section 2 of !RFC9297}}.

The `$HTTPFrame` extension point defined in {{QLOG-HTTP3}} is utilized to link
the HTTPDatagramFrame type to the existing qlog data types.

~~~ cddl
HTTPDatagramFrame = {
    frame_type: "datagram"
    quarter_stream_id: uint64
    ? raw: RawInfo
}

$HTTPFrame /= HTTPDatagramFrame
~~~
{: #httpdatagramframe-def title="HTTPDatagramFrame definition"}

# Security and Privacy Considerations

The security and privacy considerations discussed in {{QLOG-MAIN}} apply to this
document as well.


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
