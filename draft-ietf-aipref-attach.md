---
title: "Associating AI Usage Preferences with Content in HTTP"
abbrev: "AI Preference Attachment"
category: std

docname: draft-ietf-aipref-attach-latest
submissiontype: IETF
number:
date:
consensus: true
v: 3
area: "Web and Internet Transport"
workgroup: "AI Preferences"
keyword:
 - skynet training wheel
venue:
  group: "AI Preferences"
  type: "Working Group"
  mail: "ai-control@ietf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/ai-control/"
  github: "ietf-wg-aipref/drafts"
  latest: "https://ietf-wg-aipref.github.io/drafts/draft-ietf-aipref-attach.html"

author:
 -
    fullname: Gary Illyes
    organization: Google
    email: garyillyes@google.com
 -
    fullname: Martin Thomson
    organization: Mozilla
    email: mt@lowentropy.net

normative:
  FIELDS: RFC9651
  HTTP: RFC9110
  ROBOTS: RFC9309
  VOCAB:
    title: "A Vocabulary For Expressing AI Usage Preferences"
    date: draft-ietf-aipref-vocab-date
    seriesinfo:
      Internet-Draft: draft-ietf-aipref-vocab-latest
    author:
      -
        fullname: Paul Keller
        organization: Open Future
      -
        fullname: Martin Thomson
        role: editor
        organization: Mozilla

informative:
  HTTP-CACHE: RFC9111


--- abstract

Methods are defined for associating usage preferences
with content that is obtained using the HTTP protocol.
This document defines attachment methods
using the Robots Exclusion Protocol and HTTP header fields.

This document registers a "usage" namespace
for the Robots Exclusion Protocol
to allow for the inclusion of usage preferences.


--- middle

# Introduction

The automated consumption of content by crawlers and other machines
has increased significantly in recent years.
This is partly due to the training of machine-learning models.

Content creators and other stakeholders,
such as distributors,
might wish to express a preference
regarding the types of usage they consider acceptable.
Entities that might use that content
need those preferences to be stated
in a way that is easily consumed
by an automated system.

This document describes two mechanisms
for associating preferences with content:

* A Content-Usage header field
  for HTTP {{HTTP}};
  see {{header}}.
* A "usage" namespace {{!REP-NS=I-D.kelley-rep-namespaces}}
  for the Robots Exclusion Protocol
  (colloquially known as "robots.txt") {{ROBOTS}};
  see {{robots}}.

For automated systems that use HTTP to gather content,
these allow for the automated gathering of preferences
in the same way that content is obtained.


## Statements of Preference

The format of a statement of preference
is defined in the preference vocabulary {{VOCAB}}.
The preference vocabulary defines:

* a model for associating usage preferences with categories of use,
* some categories of use,
* how multiple statements of preference are combined, and
* how those preferences are turned into strings or byte sequences
  for use in a protocol.

This document only defines how the strings or byte sequences are conveyed
so that statements of preference can be associated with content.


## Examples

A server that provides content could signal preferences
about how that content is used by the Content-Usage header field
in the HTTP response:

~~~http-message
HTTP/1.1 200 OK
Date: Wed, 23 Apr 2025 04:48:02 GMT
Content-Type: text/plain
Content-Usage: train-ai=n

This is some content.
~~~

Alternatively, or additionally,
a server might include the same preference in its "robots.txt" file:

~~~
User-Agent: *
Allow: /

User-Agent: __usage_train-ai
Disallow: /
~~~


## Other Mechanisms

This document provides two general purpose methods
for associating statements of preference with assets
that are transferred using HTTP.

The mechanisms in this document can be applied to any content type,
provided that the content is obtained using HTTP (and maybe FTP).
Future work might define how preferences might be indicated
for alternative content distribution or acquisition methods,
such as email.

The attachment mechanism in this document
are intended to be complementary with other mechanisms.


### Embedded Preferences

Embedding preferences is expected to be an effective means
of associating preferences with assets,
because it ensures that metadata travels with the asset.
This document, however, does not define any specific means of embedding preferences
in content.

The main challenge with embedding preferences is that
a different method might be needed for each content type.
That is,
a different storage or serialization model of conveying the preferences
might need to be defined for each format
whether it represent audio, documents, images, video,
or other types of content.
Furthermore,
some content types,
such as plain text (`text/plain` and many other purely textual formats),
offer no standardized and interoperable means of embedding metadata.


### Registry-Based Preferences

A preferences registry is a database that stores usage preference statements
associated with both content identifiers
and a means of identifying the declaring party.
Registry-based approaches might be applicable in certain contexts,
particularly where embedding is impractical or unavailable.
Additionally, a registry might enable persistent association of preferences
across distribution channels.


## Conventions and Definitions

{::boilerplate bcp14-tagged}


# HTTP Content-Usage Header Field {#header}

The Content-Usage field is a structured field dictionary,
as defined in {{Section 3.2 of FIELDS}}.
This field follows the vocabulary and processing rules in {{VOCAB}}.

This field indicates usage preferences
regarding the content of the HTTP message.
That is, the field is representation metadata ({{Section 8.2 of HTTP}})
that applies the representation data ({{Section 8.1 of HTTP}}).
Informally, usage preferences apply to the content of a message,
not the resource.

Clients can use any preference associated with a request
to guide their usage of the content of the request.
This allows servers to express usage preferences
for assets they serve.

Servers can use any preferences associated with a request
if the content of that request
is used to answer later requests.
For example,
attached preferences for a PUT request,
where the content that is used to answer subsequent GET requests,
might be copied to the response to those GET requests.
For this to happen,
servers or resources would need to be updated
to understand this field
and then decide to use the preferences.

The Content-Usage field does not have any special effect on caching.


# Robots Exclusion Protocol Usage Namespace {#robots}

The core function of Robots Exclusion Protocol format {{ROBOTS}}
(or the "robots.txt" file)
is to describe the expectations of the server operator
about which paths can be crawled.

This document defines a "usage" namespace {{REP-NS}} that associates usage preferences
with different paths.
This namespace applies to any paths, whether or not they can be crawled;
whether a path can be crawled does not affect its associated usage preferences.

Each usage category label ({{Section 6.1 of VOCAB}}) defines a namespaced label
that can be carried by a selector (a User-Agent line)
for "robots.txt".

The `ns-label` ABNF pattern from {{Section 3.1 of REP-NS}}
is restricted as shown in {{f-abnf}}.

~~~abnf
usage-label   = "__usage_" usage-cat

usage-cat     = ns-label
              ; a usage category label from [VOCAB]
~~~
{: #f-abnf title="Usage namespace label ABNF"}

Each group contains zero or more selectors carrying a usage label.
Each usage label identifies one usage category;
the Allow and Disallow rules of the group
express the usage preference for that category.
A selector carrying a usage label
can share a group with any other selectors.

Note that a usage category label that is not an `ns-label`,
such as one that contains a digit, ".", or "*",
cannot be expressed using this namespace.


## Usage Namespace Semantics

Each group in the file applies to a set of crawlers,
identified by product token as defined in {{Section 2.2.1 of ROBOTS}}.
The Allow and Disallow rules determine what resources can be crawled,
using the rule that has the longest matching path prefix,
as defined in {{Section 2.2.2 of ROBOTS}}.

This creates a two-stage arrangement
that distinguishes acquisition and usage.
Acquisition relies on Allow/Disallow rules for product tokens;
usage preference relies on Allow/Disallow rules for usage labels.

These signals are independent.  However, consumers of preference
signals MAY use them to inform acquisition decisions; see
{{crawling}}.

In accordance with {{Section 4 of REP-NS}}, the "usage" namespace is
defined as follows:

Label syntax:
: A usage label is any usage category label from {{VOCAB}}.  In the
  grammar of {{REP-NS}}, they are `ns-label`s.

Wildcard fallback:
: No. Wildcard selectors do not match any labels in the "usage"
  namespace.  This is important because it means that existing
  robots.txt files will not be interpreted as expressing preferences
  that the authors, writing before this mechanism was deployed, could
  not have intended.

Allowed and disallowed:
: A path that is allowed for a usage label has a positive preference
  for the corresponding usage category; a path that is disallowed has
  a negative preference.

No matching selector:
: If no selector matches a usage label, no preference is expressed for
  that usage category.

No matching rule:
: As in {{ROBOTS}}.

Interaction with other labels:
: None. Selectors in a group are disjunctive, as with the
  product-token selectors of {{ROBOTS}}.

Relationship to product-token rules:
: Usage labels are policy tokens, as described in {{Section 6 of
  REP-NS}}.  Consumers may document that product tokens act as
  proprietary policy tokens.  Where they do, those more specific
  signals take precedence over the more general ones described in this
  draft; see {{precedence}}.

A crawlers can cache a "robots.txt" file for up to 24 hours,
following HTTP Cache-Control semantics defined in {{HTTP-CACHE}};
see {{Section 2.4 of ROBOTS}} for details.
Updates to preferences within the period that a file is cached
might not be visible.


## Processing Usage Labels

To process a usage label, a parser looks up the path and the label as
described in {{Section 5 of REP-NS}}.  This requires that the label is
matched exactly, apart from case, and that groups with a wildcard
selector are not consulted for the label.  A parser repeats this
process for each usage category whose preference is sought.

{:aside}
> Note that general-purpose robots.txt parsers
> often apply the wildcard fallback to any label,
> and some match labels by prefix or substring;
> see {{Section 5 of REP-NS}}.

A selector carrying a namespaced label in the "usage" namespace that
does not correspond to a known usage category is ignored.

If no group contains a usage label for a usage category (i.e., there
is no `User-Agent: __usage_<label>` line in the file), then no
preference has been expressed for that category.  If such a file
contains a proprietary policy token ({{Section 2 of REP-NS}}),
consumers SHOULD interpret that token as they would in the absence of
this document, but MUST NOT treat it as expressing a preference in the
context of this document (see {{precedence}}).

The usage preferences are mapped to the data model defined in
{{Section 3 of VOCAB}}.  The processing rules from {{Section 5 of
VOCAB}} apply.


## When Preferences Apply

A crawler that fetches resources uses the copy of "robots.txt"
that is current at the time of the fetch
to determine which usage preferences apply to those resources.
{{Section 2.4 of ROBOTS}} defines how a "robots.txt" file can be cached.

This means that updates to "robots.txt" do not retroactively apply
to resources.
Changes to "robots.txt" that affect usage preferences
therefore only apply
after a crawler has retrieved the updated "robots.txt"
and subsequently retrieved the affected resource again.


## Effects on Crawling Behavior {#crawling}

Usage labels are not directives for crawling behavior.

A consumer MAY use preferences to inform crawling.
For example, if it crawls only for purposes covered by a usage label,
it MAY refrain from retrieving resources
for which a preference against those purposes has been expressed.

If a service operator wishes to limit crawling,
it MAY use groups for crawlers' product tokens in "robots.txt" to do so.


## Precedence {#precedence}

Specific expressions SHOULD take precedence over general ones.

Consumers MAY document that they interpret rules
that match proprietary policy tokens
as being more specific than usage labels.

Private agreements MAY override preferences expressed via this mechanism.


## Example

{{f-ex-robots}} shows a simple "robots.txt" document.

~~~
User-Agent: *
Allow: /
Disallow: /never/

User-Agent: ExampleBot
Allow: /

User-Agent: __usage_train-ai
Disallow: /
Allow: /ai-ok/
~~~
{: #f-ex-robots title="Example robots.txt file"}

A crawler that identifies as "ExampleBot" uses the second group.
That crawler would be able to obtain all content.

All other crawlers use the first group.
This allows crawling of all content other than resources under "/never/".

Usage preferences are taken from the third group,
regardless of which crawler obtains a resource.
Resources under "/ai-ok/" have an associated usage preference of "train-ai=y"
and all other resources have a usage preference of "train-ai=n".
Preferences for other usage categories are unknown.

| Path           | Crawl   | Usage Preference |
|:---------------|:-------:|:-----------------|
| /test          |  yes    | train-ai=n       |
| /never/test    |  no     | train-ai=n       |
| /ai-ok/test    |  yes    | train-ai=y       |
{: #t-example title="Sample of usage preferences for different paths"}

The "Crawl" column describes crawlers other than "ExampleBot".

Note that, unlike rules for product tokens,
usage preferences cannot be scoped to a particular crawler;
see {{Section 3.2 of REP-NS}}.


# Security Considerations

Processing usage preferences involves the parsing of text
that is produced by potential adversaries.
Different guidelines for robust parsing can be found in
{{Section 6 of FIELDS}} and {{Section 17 of HTTP}}.

{{Section 3 of ROBOTS}} describes security considerations for "robots.txt".
A "robots.txt" file can be up to 500KiB of text.
This specification does not increase this limit.
Because usage labels can share groups with other selectors,
expressing usage preferences need not add many lines to a "robots.txt" file.


# IANA Considerations

The Content-Usage HTTP header field defined in {{header}}
is added to the "HTTP Field Name" registry
established in {{Section 18.4 of HTTP}}:

Field Name:
: Content-Usage

Status:
: permanent

Structured Type:
: Dictionary

Reference:
: {{header}}

Comments:
: None
{: spacing="compact"}

The "usage" namespace defined in {{robots}}
is added to the "REP Namespaces" registry
established in {{Section 10.1 of REP-NS}}:

Namespace:
: usage

Label syntax:
: Usage category labels from {{Section 6.1 of VOCAB}}
  that conform to the `ns-label` rule; see {{robots}}

Wildcard fallback:
: no

Reference:
: {{robots}}

Change controller:
: IETF (iesg@ietf.org)
{: spacing="compact"}


--- back
