---
title: >
  CoRE Resource Directory: DNS-SD mapping
docname: draft-ietf-core-rd-dns-sd-latest
date: 2017-03-31
stand_alone: true
ipr: trust200902
cat: std
pi:
  strict: 'yes'
  toc: 'yes'
  tocdepth: '3'
  symrefs: 'yes'
  sortrefs: 'yes'
  compact: 'yes'
  comments: yes
  subcompact: 'no'
  iprnotified: 'no'
area: Internet
wg: CoRE
kw: CoRE, Web Linking, Resource Discovery, Resource Directory
author:
- ins: C. Amsüss
  name: Christian Amsüss
  organization: Energy Harvesting Solutions
  street: Hollandstr. 12/4
  code: '1020'
  country: Austria
  phone: "+43-664-9790639"
  email: c.amsuess@energyharvesting.at
  role: editor
- ins: K. E. Lynn
  name: Kerry Lynn
  org: Verizon Labs
  street: 50 Sylvan Rd
  city: Waltham
  region: MA
  code: '02451'
  country: USA
  phone: "+1 781 296 9722"
  email: kerlyn@ieee.org
- ins: M. Koster
  name: Michael Koster
  org: SmartThings
  street: 665 Clyde Avenue
  city: Mountain View
  code: '94043'
  country: USA
  phone: "+1-707-502-5136"
  email: Michael.Koster@smartthings.com
- ins: P. van der Stok
  name: Peter van der Stok
  org: consultant
  abbrev: consultant
  phone: "+31-492474673 (Netherlands), +33-966015248 (France)"
  email: consultancy@vanderstok.org
  uri: www.vanderstok.org
normative:
  RFC6690:
  RFC2119:
#  RFC3986:
#  RFC5226:
  RFC5988:
  RFC6335: portreg
  RFC6570:
  RFC6763: dnssd
#  RFC7396:
#  I-D.ietf-core-links-json: links
  I-D.ietf-core-resource-directory: rd
informative:
  RFC7252:
#  RFC7390:
#  RFC6775:
#  RFC7230:
  RFC3629: utf8
  RFC5198: nvt-utf8
  RFC1123: hostreq
  RFC1034: dns1

--- abstract

TBD

--- middle

# Introduction

TBD ... {{RFC7252}} ... {{-rd}} ... DNS-SD

## Terminology {#terminology}

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT",
"SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL"
in this
document are to be interpreted as described in {{RFC2119}}. The
term "byte" is used in its now customary sense as a synonym for "octet".

This specification requires readers to be familiar with all the terms and
concepts that are discussed in {{RFC5988}} and {{RFC6690}}. Readers should
also be familiar with the terms and concepts discussed in {{RFC7252}}.  To
describe the REST interfaces defined in this specification, the URI Template
format is used {{RFC6570}}.

This specification makes use of the terminology of {{-rd}}.

This specification makes use of the following additional terminology:

TBD:
: TBD

TBD:
: TBD


# New Link-Format Attributes {#attributes}

When using the CoRE Link Format to describe resources being discovered by
or posted to a resource directory service, additional information about those
resources is useful. This specification defines the following new attributes
for use in the CoRE Link Format {{RFC6690}}:

~~~~ ABNF
   link-extension    = ( "ins" "=" (ptoken | quoted-string) )
                       ; The token or string is max 63 bytes
   link-extension    = ( "exp" )
~~~~

## Resource Instance attribute 'ins'

The Resource Instance "ins" attribute is an
identifier for this resource, which makes it possible
to distinguish it from other similar resources. This attribute is similar
in use to the &lt;Instance> portion of a DNS-SD record (see {{cheshire}}, and SHOULD be unique across resources with the same Resource Type attribute
in the domain it is used. A Resource Instance might be a descriptive string
like "Ceiling Light, Room 3", a short ID like "AF39" or a unique UUID or
iNumber. This attribute is used by a Resource Directory to distinguish between
multiple instances of the same resource type within the directory.

This attribute MUST be no more than 63 bytes in length. The resource identifier
attribute MUST NOT appear more than once in a link description.  This attribute MAY be used as a query parameter in the RD Lookup Function Set defined in Section 7.


## Export attribute 'exp'

The Export "exp" attribute is used as a flag to indicate that a link description
MAY be exported by a resource directory to external directories.

The CoRE Link Format is used for many purposes between CoAP endpoints. Some
are useful mainly locally, for example checking the observability of a resource
before accessing it, determining the size of a resource, or traversing dynamic
resource structures. However, other links are very useful to be exported
to other directories, for example the entry point resource to a functional
service.  This attribute MAY be used as a query parameter in the RD Lookup Function Set defined in Section 7.

# DNS-SD Mapping {#dns-sd}

CoRE Resource
Discovery is intended to support fine-grained discovery of hosted
resources, their attributes, and possibly other resource relations {{RFC6690}}. In contrast, service discovery generally refers to a coarse-grained
resolution of an endpoint's IP address, port number, and protocol.

Resource and service discovery are complementary in the case of large
networks, where the latter can facilitate scaling.  This document
defines a mapping between CoRE Link Format attributes and DNS-Based
Service Discovery {{RFC6763}} fields that permits
discovery of CoAP services by either method.

## DNS-based Service discovery {#cheshire}

DNS-Based Service Discovery (DNS-SD) defines a conventional method of
configuring DNS PTR, SRV, and TXT resource records to facilitate
discovery of services (such as CoAP servers in a subdomain) using the
existing DNS infrastructure.  This section gives a brief overview of
DNS-SD; see {{RFC6763}} for a detailed
specification.

DNS-SD service names are limited to 255 octets and are of the form:

Service Name = &lt;Instance>.&lt;ServiceType>.&lt;Domain>.

The service name is the label of SRV/TXT resource records. The SRV RR specifies
the host and the port of the endpoint. The TXT RR provides additional information
in the form of key/value pairs.

The &lt;Domain> part of the service name is identical to the global (DNS
subdomain) part of the authority in URIs that identify servers or groups
of servers.

The &lt;ServiceType> part is composed of at least two labels.  The first
label of the pair is the application protocol name {{RFC6335}} preceded by an
underscore character.  The second label indicates the transport and is always
"_udp" for UDP-based CoAP services.  In cases where narrowing the scope of
the search may be useful, these labels may be optionally preceded by a
subtype name followed by the "_sub" label.  An example of this more specific
&lt;ServiceType> is "light._sub._dali._udp".

A default &lt;Instance> part of the service name may be set at the factory
or during the commissioning process.  It SHOULD uniquely identify an instance
of &lt;ServiceType> within a &lt;Domain>.  Taken together, these three
elements comprise a unique name for an SRV/ TXT record pair within the DNS
subdomain.

The granularity of a service name MAY be that of a host or group, or it could
represent a particular resource within a CoAP server.  The SRV record
contains the host name (AAAA record name) and port of the service while
protocol is part of the service name.  In the case where a service name
identifies a particular resource, the path part of the URI must be carried in
a corresponding TXT record.

A DNS TXT record is in practice limited to a few hundred octets in length,
which is indicated in the resource record header in the DNS response message.
The data consists of one or more strings comprising a key=value pair.  By
convention, the first pair is txtver=&lt;number> (to support different
versions of a service description).


## mapping ins to &lt;Instance> {#ins}

The Resource Instance "ins" attribute maps to the &lt;Instance> part of a
DNS-SD service name.  It is stored directly in the DNS as a single DNS label
of canonical precomposed UTF-8 {{RFC3629}} "Net-Unicode" (Unicode
Normalization Form C) {{RFC5198}} text.  However, to the extent that the
"ins" attribute may be chosen to match the DNS host name of a service, it
SHOULD use the syntax defined in Section 3.5 of {{RFC1034}} and Section 2.1
of {{RFC1123}}.

The &lt;Instance> part of the name of a service being offered on the network
SHOULD be configurable by the user setting up the service, so that he or she
may give it an informative name.  However, the device or service SHOULD NOT
require the user to configure a name before it can be used.  A sensible
choice of default name can allow the device or service to be accessed in many
cases without any manual configuration at all.  The default name should be
short and descriptive, and MAY include a collision-resistant substring such
as the lower bits of the device's MAC address, serial number, fingerprint, or
other identifier in an attempt to make the name relatively unique.

DNS labels are currently limited to 63 octets in length and the
entire service name may not exceed 255 octets.


## Mapping rt to &lt;ServiceType> {#exp}

The resource type "rt" attribute is mapped into the &lt;ServiceType> part of
a DNS-SD service name and SHOULD conform to the reg-rel-type production of
the Link Format defined in Section 2 of {{RFC6690}}.  The "rt" attribute MUST
be composed of at least a single Net-Unicode text string, without underscore
'_' or period '.' and limited to 15 octets in length, which represents the
application protocol name.  This string is mapped to the DNS-SD
&lt;ServiceType> by prepending an underscore and appending a period followed
by the "_udp" label.  For example, rt="dali" is mapped into "_dali._udp".

The application protocol name may be optionally followed by a period
and a service subtype name consisting of a Net-Unicode text string,
without underscore or period and limited to 63 octets.  This string
is mapped to the DNS-SD &lt;ServiceType> by appending a period followed
by the "_sub" label and then appending a period followed by the
service type label pair derived as in the previous paragraph.  For
example, rt="dali.light" is mapped into "light._sub._dali._udp".

The resulting string is used to form labels for DNS-SD records which
are stored directly in the DNS.


## Domain mapping {#domain}

DNS domains may be derived from the "d" attribute. The domain attribute may
be suffixed with the zone name of the authoritative DNS server to generate
the domain name. The "ep" attribute is prefixed to the domain name to generate
the FQDN to be stored into DNS with an AAAA RR.


## TXT Record key=value strings {#TXT}

A number of {{RFC6763}} key/value pairs are derived from link-format
information, to be exported in the DNS-SD as key=value strings in a
TXT record ({{RFC6763}}, Section 6.3).

The resource &lt;URI> is exported as key/value pair "path=&lt;URI>".

The Interface Description "if" attribute is exported as key/value
pair "if=&lt;Interface Description>".

The DNS TXT record can be further populated by importing any other
resource description attributes as they share the same key=value
format specified in Section 6 of {{RFC6763}}.


## Importing resource links into DNS-SD {#import}

Assuming the ability to query a Resource Directory or multicast a GET
(?exp) over the local link, CoAP resource discovery may be used to
populate the DNS-SD database in an automated fashion.  CoAP resource
descriptions (links) can be exported to DNS-SD for exposure to
service discovery by using the Resource Instance attribute as the
basis for a unique service name, composed with the Resource Type as
the &lt;ServiceType>, and registered in the correct &lt;Domain>.  The agent
responsible for exporting records to the DNS
zone file SHOULD be authenticated to the DNS server.
The following example, using the example lookup location /rd-lookup, shows an agent discovering a resource to be
exported:

~~~~
   Req: GET /rd-lookup/res?exp

   Res: 2.05 Content
   <coap://[FDFD::1234]:5683/light/1>;
     exp;rt="dali.light";ins="Spot";
               d="office";ep="node1"

~~~~

The agent subsequently registers the following DNS-SD RRs, assuming a zone
name "example.com" prefixed with "office":


~~~~
node1.office.example.com.          IN AAAA        FDFD::1234
_dali._udp.office.example.com      IN PTR
                          Spot._dali._udp.office.example.com
light._sub._dali._udp.example.com  IN PTR
                          Spot._dali._udp.office.example.com
Spot._dali._udp.office.example.com IN SRV  0 0 5683
                          node1.office.example.com.
Spot._dali._udp.office.example.com IN TXT
                          txtver=1;path=/light/1
~~~~

In the above figure the Service Name is chosen as Spot._dali._udp.office.example.com
without the light._sub service prefix. An alternative Service Name would
be: Spot.light._sub._dali._udp.office.example.com.



# Examples
 
## DNS entries {#dns-en}


It may be profitable to discover the light groups for applications, which are unaware ot the existence of the RD. An agent needs to query the
RD to return all groups which are exported to be inserted into DNS.


~~~~
   Req: GET /rd-lookup/gp?exp

   Res: 2.05 Content
   <coap://[FF05::1]/>;exp;gp="grp_R2-4-015;ins="grp1234";
ep="lm_R2-4-015_wndw";
ep="lm_R2-4-015_door

~~~~

The group with FQDN grp_R2-4-015.bc.example.com can be entered into the DNS
by the agent. The accompanying instance name is grp1234. The &lt;ServiceType>
is chosen to be _group._udp. The agent enters the following RRs into the
DNS.


~~~~
grp_R2-4-015.bc.example.com.        IN AAAA            FF05::1
_group._udp.bc.example.com          IN PTR
                            grp1234._group._udp.bc.example.com
grp1234._group._udp.bc.example.com  IN SRV  0 0 5683
                             grp_R2-4-015_door.bc.example.com.
grp1234._group._udp.bc.example.com  IN TXT
                                     txtver=1;path=/light/grp1
~~~~

From then on applications, not familiar with the existence of the RD, can use DNS to access the lighting group.

# IANA considerations

TBD

# Security considerations

TBD

--- back

# Acknowledgements
{: numbered='no'}

This document was split off from {{-rd}}.  TODO: Copy over relevant acknowledgements.
