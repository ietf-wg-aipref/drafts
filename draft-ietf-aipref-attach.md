---
title: "Indicating Preferences Regarding Content Usage"
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
need those preferences to be expressed
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


## Preference Expressions

The format of preference expressions
is defined in the preference vocabulary {{VOCAB}}.
The preference vocabulary defines:

* what preferences can be expressed,
* how multiple expressions of preference are combined, and
* how those preferences are turned into strings or byte sequences
  for use in a protocol.

This document only defines how the strings or byte sequences are conveyed
so that the preferences can be associated with content.


## Examples

A server that provides content using HTTP could signal preferences
about how that content is used with the Content-Usage header field
as follows:


~~~http-message
200 OK
Date: Wed, 23 Apr 2025 04:48:02 GMT
Content-Type: text/plain
Content-Usage: ai=n

This is some content.
~~~

Alternatively, or additionally,
a server might include the same directive in its "robots.txt" file:

~~~
User-Agent: *
Allow: /
Content-Usage: ai=n
~~~


## Embedded Preferences

This document does not define a means of embedding preferences
in content.
Embedding preferences is expected to be an effective means
of associating preferences with content,
because it ensures that metadata is always associated with content.

The main challenge with embedding is that
a different method is needed for each content type.
That is,
a different means of conveying preferences
needs to be defined for each audio, documents, images, video,
or other content format.
Furthermore,
some content types,
such as plain text (`text/plain`),
offer no universal means of carrying metadata.
Though preferences might still be embedded in content with these formats,
those preferences would not be reliably accessible to an automated system.

The mechanisms in this document are therefore universal,
in the sense that they apply to any content type.
They are not universal
in that they rely on the content being obtained using HTTP
(and maybe FTP).

Future work might define how preferences might be indicated
for alternative content distribution or acquisition methods,
such as email.


## Conventions and Definitions

{::boilerplate bcp14-tagged}


# HTTP Content-Usage Header Field {#header}

The Content-Usage field is a structured field dictionary,
as defined in {{Section 3.2 of FIELDS}}.
This field follows the vocabulary and processing rules in {{VOCAB}}.
<!-- TODO: confirm with VOCAB -->

This field indicates usage preferences
regarding the content of the HTTP message.
That is, the representation data,
as defined in {{Section 8.1 of HTTP}},
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

A Content-Usage rule is added to the set of potential rules
that can be included in a Group
for the Robots Exclusion Protocol format {{ROBOTS}}.

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

Note that the usage preference expression encoding
does not use an ABNF definition,
relying instead on the definitions in {{FIELDS}}.


## Content-Usage Rule Semantics

Each group in the file applies to a set of crawlers,
identified by product token as defined in {{Section 2.2.1 of ROBOTS}}.
The Allow and Disallow rules determine what resources can be crawled,
using the rule that has the longest matching path prefix,
as defined in {{Section 2.2.2 of ROBOTS}}.

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
The remainder of the rule value is the usage preference expression.
If a path is absent,
the entire rule value is the usage preference expression.

The usage preference is encoded using the exemplary format
defined in {{Section 6 of VOCAB}}.
The parsing and processing rules from {{Sections 6 and 7 of VOCAB}} apply.

Note that a usage preference expression is processed as a sequence of bytes,
rather than Unicode text; see {{Section 6.3 of VOCAB}}.


## Example

{{f-ex-robots}} shows a simple "robots.txt" document.

~~~
User-Agent: *
Allow: /
Disallow: /never/
Content-Usage: ai=n
Content-Usage: /ai-ok/ ai=y

User-Agent: ExampleBot
Allow: /
Content-Usage: ai=y
~~~
{: #f-ex-robots title="Example robots.txt file"}

A crawler that identifies as "ExampleBot" uses the second group.
That crawler would be able to obtain all content
and apply usage preferences of "ai=y" as defined in {{VOCAB}}.

All other crawlers use the first group.
This allows crawling of all content other than resources under "/never/".
Of those resources,
those under "/ai-ok/" have an associated usage preference of "ai=y"
and all other resources have a usage preference of "ai=n".

| Path           | Crawl   | Usage Preference |
|:---------------|:-------:|:-----------------|
| /test          |  yes    | ai=n             |
| /never/test    |  no     | N/A              |
| /ai-ok/test    |  yes    | ai=y             |
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

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
