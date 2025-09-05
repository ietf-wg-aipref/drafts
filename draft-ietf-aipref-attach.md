---
title: "Associating AI Usage Preferences with Content in HTTP"
abbrev: "AI Preference Attachment"
category: std

docname: draft-ietf-aipref-attach-latest
submissiontype: IETF
number:
updates: 9309
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
  URI: RFC3986
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

Content creators and other stakeholders might wish to signal
their preferences about how their content
might be consumed by automated systems.
This document defines how preferences can be signaled
as part of the acquisition of content in HTTP.

This document updates RFC 9309
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
* A Content-Usage directive
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
a server might include the same directive in its "robots.txt" file:

~~~
User-Agent: *
Allow: /
Content-Usage: train-ai=n
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
of associating preferences with content,
because it ensures that metadata is always associated with content.
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
such as plain text (`text/plain`),
offer no standardized means of embedding preferences.


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

Servers MUST retain any preferences associated with a request
if the content of that request
is used to answer later requests.
For example,
the content of a PUT request that is used
to answer subsequent GET requests.
Note that servers that have not been updated to understand this field
will not comply with this requirement.

The Content-Usage field does not have any special effect on caching.


# Robots Exclusion Protocol Content-Usage Rule {#robots}

The core function of Robots Exclusion Protocol format {{ROBOTS}}
(or the "robots.txt" file)
is to describe the expectations of the server operator
about which paths can be crawled.
This document adds a new rule that associates usage preferences
with different paths.
This new rule applies to any paths that can be crawled;
paths that cannot be crawled have no associated usage preferences.

A Content-Usage rule is added to the set of potential rules
that can be included in a Group
for "robots.txt".

The `rule` ABNF pattern from {{Section 2.2 of ROBOTS}}
is extended as shown in {{f-abnf}}.

~~~abnf
rule =/ content-usage

content-usage = *WS "content-usage" *WS ":" *WS
                [ path-pattern 1*WS ] usage-pref EOL
usage-pref    = <usage preference vocabulary from [VOCAB]>
~~~
{: #f-abnf title="Extended robots.txt ABNF"}

Each group contains zero or more Content-Usage rules.
Each Content-Usage rule consists of a path
and a usage preference.
The path might be absent or empty;
if a path present,
a SP or HTAB separates it from the usage preference.

Note that the statement of preference encoding
does not use an ABNF definition,
relying instead on the definitions in {{FIELDS}}.


## Content-Usage Rule Semantics

Each group in the file applies to a set of crawlers,
identified by product token as defined in {{Section 2.2.1 of ROBOTS}}.
The Allow and Disallow rules determine what resources can be crawled,
using the rule that has the longest matching path prefix,
as defined in {{Section 2.2.2 of ROBOTS}}.

This creates a two-stage arrangement
that distinguishes acquisition and usage.
Acquisition relies on Allow/Disallow rules;
usage preference relies on Content-Usage rules.

Any Content-Usage rules determine the usage preferences for resources
using the same path prefix matching rules as defined for Allow and Disallow.
That is, the path prefix length is determined by counting the number of bytes
in the encoded path.

Usage preferences apply only to those resources that can be crawled
according to Allow/Disallow rules;
no preferences are implied for resources that are disallowed.

Paths specified for Content-Usage rules use the same percent-encoding rules
as used for Allow/Disallow rules,
as defined in {{Section 2.1 of URI}}.
In particular, SP (U+20) and HTAB (U+09) characters need to be replaced
with "%20" and "%09" respectively.

The ordering of rules in a group carries no semantics.
Thus, Content-Usage rules can be interleaved
with Allow and Disallow rules.

If there are Content-Usage rules that have identical paths
and conflicting usage preferences,
these preferences apply separately
according to the process defined in {{Section 7.1 of VOCAB}}.
Note that this differs from the Allow/Disallow rules,
where a conflict leads to the more permissive option,
allowing crawling.

A crawlers can cache a "robots.txt" file for up to 24 hours,
following HTTP Cache-Control semantics defined in {{HTTP-CACHE}};
see {{Section 2.4 of ROBOTS}} for details.
Updates to preferences within the period that a file is cached
might not be visible.


## Processing Content-Usage Rules

To process a Content-Usage rule,
a parser identifies lines with the "Content-Usage" label.
This requires that SP and HTAB characters are ignored,
before and after the label,
in addition to before and after the COLON (":", U+3A) separator.

The remainder of the line -
up to either the first CR (U+0D), LF (U+0A), or octothorpe ("#", U+23) -
is the rule value.

The first character of the rule value will be "/" (U+2F)
if a non-empty path is specified.
Paths always start with a "/" character,
so a rule value that starts with any other character
indicates that the path is absent.

If a path is specified,
the path ends immediately before the first SP (U+20) or HTAB ("U+09") character.
The remainder of the rule value is the statement of preference.
If a path is absent,
the entire rule value is the statement of preference.

The usage preference is encoded using the exemplary format
defined in {{Section 6 of VOCAB}}.
The parsing and processing rules from {{Sections 6 and 7 of VOCAB}} apply.

Note that a statement of preference is processed as a sequence of bytes,
rather than Unicode text; see {{Section 6.3 of VOCAB}}.


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


## Example

{{f-ex-robots}} shows a simple "robots.txt" document.

~~~
User-Agent: *
Allow: /
Disallow: /never/
Content-Usage: train-ai=n
Content-Usage: /ai-ok/ train-ai=y

User-Agent: ExampleBot
Allow: /
Content-Usage: train-ai=y
~~~
{: #f-ex-robots title="Example robots.txt file"}

A crawler that identifies as "ExampleBot" uses the second group.
That crawler would be able to obtain all content
and apply usage preferences of "ai=y" as defined in {{VOCAB}}.

All other crawlers use the first group.
This allows crawling of all content other than resources under "/never/".
Of those resources,
those under "/ai-ok/" have an associated usage preference of "train-ai=y"
and all other resources have a usage preference of "train-ai=n".

| Path           | Crawl   | Usage Preference |
|:---------------|:-------:|:-----------------|
| /test          |  yes    | train-ai=n       |
| /never/test    |  no     | N/A              |
| /ai-ok/test    |  yes    | train-ai=y       |
{: #t-example title="Sample of usage preferences for different paths"}


# Security Considerations

Processing usage preferences involves the parsing of text
that is produced by potential adversaries.
Different guidelines for robust parsing can be found in
{{Section 6 of FIELDS}} and {{Section 17 of HTTP}}.

{{Section 3 of ROBOTS}} describes security considerations for "robots.txt".
A "robots.txt" file can be up to 500KiB of text.
This specification does not increase this limit.


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


--- back
