---
title: "Indicating Preferences Regarding Content Usage"
abbrev: "Content Usage Preferences"
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
  VOCAB: I-D.ietf-aipref-vocab

informative:


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
Content-Usage: ai=n
Allow: /
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


# Robots Exclusion Protocol Content-Usage Directive {#robots}

A Content-Usage directive is added to the Group definition
in the Robots Exclusion Protocol format {{ROBOTS}}.

That is, the ABNF is extended as follows:

~~~abnf
group = startgroupline *(startgroupline / emptyline)
        [content-usage] ; <-- NEW
        *(rule / emptyline)

content-usage = *WS "content-usage" *WS ":" *WS usage-pref
usage-pref    = <usage preference vocabulary; see [VOCAB]>
~~~

This directive updates the definition of a group to be more expansive.
Where a group was previously a set of user-agents
(either "*" or a set of one or more identifiers),
a Group is updated to include zero or one Content-Usage preferences.

## Processing Multiple Groups

The effect of this change is that
a crawler might need to consider multiple groups.
A crawler needs to consider this both to decide
whether content can be requested
and to determine what preferences apply to content.

Rather than looking for a group based on a specific User-Agent identifier,
such as "ExampleBot",
then falling back to the wildcard group ("*"),
a crawler might have multiple groups,
each with a different set of preferences.

Where there are multiple groups,
a crawler first looks for groups with a matching User-Agent identifer.
If any groups match the crawler identity
(as defined in {{Section 2.2.1 of ROBOTS}}),
all matching groups are considered.
If there are no matching groups,
all groups that include a User-Agent of "*" are considered.

In determining which group applies for a given resource,
the crawler evaluates each group in turn.
Any group for which the resource is disallowed
(as defined in {{Section 2.2.2 of ROBOTS}})
is excluded.
If all groups are excluded in this way,
the resource is not crawled.

If any group allows the crawling of the resource,
content can be retrieved.
If multiple groups allow crawling,
the usage preference from the group
with the longest Allow rule match
applies to that content.

For example, given the following "robots.txt" document:

~~~
User-Agent: *
Content-Usage: ai=n
Allow: /
Disallow: /never/

User-Agent: *
Content-Usage: ai=y
Allow: /ai-ok/
Disallow: /

User-Agent: ExampleBot
Content-Usage: ai=y
Allow: /
~~~

A crawler that identifies as "ExampleBot"
would be able to obtain all content
and apply preferences of "ai=y"
(processed as defined in {{VOCAB}}).

All other crawlers would use the same two groups.
The first group allows the retrieval of most resources,
excluding resources starting with "/never/",
and applies a usage preference of "ai=n" across those resources.
The second group creates a specific rule
for resources under "/ai-ok",
where the usage preference is "ai=y".
This might result in the following outcome after crawling:


| Path           | Allowed | Saved Preference |
|:---------------|:-------:|:-----------------|
| /test          |  yes    | ai=n             |
| /never/test    |  no     | n/a              |
| /ai-ok/test    |  yes    | ai=y             |


# Security Considerations

Processing usage preferences involves the parsing of text
that is produced by potential adversaries.
Different guidelines for robust parsing can be found in
{{Section 6 of FIELDS}} and {{Section 17 of HTTP}}.

{{Section 3 of ROBOTS}} describes security considerations for "robots.txt".
A "robots.txt" file can be up to 500KiB of text.
This specification does not increase this limit.


# IANA Considerations

TODO request registration of field

--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
