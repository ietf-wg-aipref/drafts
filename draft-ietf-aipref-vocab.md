---
title: "A Vocabulary For Expressing AI Usage Preferences"
abbrev: "AI Preference Vocabulary"
category: std

docname: draft-ietf-aipref-vocab-latest
submissiontype: IETF
number:
date:
consensus: true
v: 3
area: "Web and Internet Transport"
workgroup: "AI Preferences"
keyword:
  - AI Preferences
  - Opt-Out
  - Vocabulary
venue:
  group: "AI Preferences"
  type: "Working Group"
  mail: "ai-control@ietf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/ai-control/"
  github: "ietf-wg-aipref/drafts"
  latest: "https://ietf-wg-aipref.github.io/drafts/draft-ietf-aipref-vocab.html"

author:
  -
    fullname: Paul Keller
    organization: Open Future
    email: paul@openfuture.eu
  -
    fullname: Martin Thomson
    role: editor
    organization: Mozilla
    email: mt@lowentropy.net

normative:
  ASCII: RFC0020
  FIELDS: RFC9651

informative:
  UTF8: RFC3629
  ATTACH:
    title: "A Vocabulary For Expressing AI Usage Preferences"
    date: draft-ietf-aipref-attach-date
    seriesinfo:
      Internet-Draft: draft-ietf-aipref-attach-latest
    author:
      -
        fullname: Gary Illyes
        organization: Google
      -
        fullname: Martin Thomson
        role: editor
        organization: Mozilla

...

--- abstract

This document defines a vocabulary for expressing preferences
regarding how digital assets are used by automated processing systems.
This vocabulary allows for the declaration
of restrictions or permissions for use of digital assets by such systems.

--- note_Note_to_Readers

As detailed below, this is a working document. Its contents DO NOT REFLECT CONSENSUS of the Working Group either in whole or part. Presense or absense of any particular text does not indicate consensus, and this document is published solely as a basis of further discussion.

--- middle

# Introduction

This document defines a vocabulary of preferences
regarding how automated systems process digital assets --
in particular, the training and use of AI models.
This vocabulary can be used to describe
the types of uses that a declaring party may wish to explicitly restrict or allow.

The vocabulary is intended to be used
in jurisdictions where expressing preferences results in legal obligations,
as well as where there are no associated legal obligations.
In either case, expressing preferences is without prejudice to applicable laws,
including the applicability of exceptions and limitations to copyright.

{{model}} defines the data model for AI Preferences.
{{vocab}} defines the terms of the vocabulary.
{{usage}} explains how to use AI Preferences in a data processing application,
and {{format}} describes a way to serialize preferences into a string.
{{usage}} describes a process for determining the preference for a category of use.

{{ATTACH}} defines mechanisms to associate preferences with assets.
Other means of association might be defined separately in the future.


# Conventions and Definitions

{::boilerplate bcp14-tagged}

This document uses the following terms:

{: newline="true" spacing="compact"}
Asset:
: A digital file or stream of data, usually with associated metadata.

Declaring party:
: The entity that expresses a preference with regards to an Asset.

Inference:
: All use of an AI model that is used to generate synthetic content in one or more modalities, except use that modifies the learned parameters of the model.

Selection step:
: An operation, performed by any party or process other than the human user on whose behalf the inference occurs, that identifies one or more assets in response to a request, including searching, browsing, ranking, recommending, or resolving a description to an asset. Identifying an asset from among assets that the user previously provided is not a selection step.


# Statements of Preference {#model}

NOTE: This section does not yet have consensus; see "Note to Readers" above.

The vocabulary is a set of categories,
each of which is defined to cover a class of usage for assets.
{{vocab}} defines the core set of usage categories in detail.

A statement of preference -- or usage preference -- is made about an asset.
A statement of preference follows a simple data model where a preference
is assigned to each of the categories of use in the vocabulary.
A preference is either to allow or disallow
the usage associated with the category.

A statement of preference can indicate preferences
about some, all, or none of the categories from the vocabulary.
This can mean that the preference is unknown for some usage categories.

After processing a statement of preferences
the recipient associates each category of use
one of three preference values: "allowed", "disallowed", or "unknown".
In the absence of a statement of preference,
all usage categories are assigned a preference value of "unknown".

The process for consulting a statement of preference is defined in {{usage}}.

Different declaring parties might each make their own statement of preference
regarding a particular asset.
The process for managing multiple statements of preference is defined in {{combine}}.

An exemplary syntax for statements of preference is defined in {{format}}.


## Understanding Preferences {#understanding}

This document and {{ATTACH}}
describe how statements of preference are associated with assets.

The goal of these specifications is to ensure
that the recipient of an asset knows
what preferences have been associated with the asset.
What a recipient then does with that information depends on many factors;
see {{applicability}}.

There are also some caveats that need to be considered
as it relates to understanding what the preferences for a given asset are
(as opposed to what actions might then follow).

A recipient can only apply preferences it understands.
Recipients that implement this specification
will understand the vocabulary terms defined in {{vocab}},
but they might not understand extensions; see {{extension}}.

A recipient can only understand preferences expressed
through mechanisms it has implemented.
Those methods might be limited to those in {{ATTACH}}
or it could also include other methods (see {{Section 1.3 of ATTACH}}).
If a preference is associated with an asset
using a method the recipient does not understand or recognize,
the recipient will remain ignorant of that preference.

Depending on the way in which preferences are expressed,
a recipient might be unable to tell the source of the preference.
Unless the source is explicitly identified,
no assumptions can be made about where a preference originates.
For example, preferences in robots.txt (see {{Section 3 of ATTACH}})
only implies that a server
is the source of those preferences.

A method of associating preferences with assets
could explicitly define the source of the preferences,
which might involve authentication.
Otherwise, no assumptions can be made about the origin of preferences.
The apparent source of preferences
could be representing their own preferences,
the preferences of others,
or the synthesis of multiple preferences from different sources.


## Applying Preferences {#applicability}

This specification enables the expression of a defined set of preferences that
can be communicated and interoperably understood. Readers of this
specification should understand that it does not:

- ensure that preferences are followed;
- address if, how, or when preferences should be followed or not-followed;
- address technical, legal, contractual, or other mechanisms that might create
  a stronger requirement to follow or not follow preferences;
- consider situations or purposes that might justify following or not-following
  expressed preferences.

An entity that receives usage preferences has a choice whether to follow those
preferences. This specification does not determine how that choice is made.
Whether and under which circumstances a preference is followed is outside the
scope of this specification.


# Vocabulary Definition {#vocab}

NOTE: This section does not yet have consensus; see "Note to Readers" above.

This section defines the categories of use in the vocabulary.

These categories describe concrete, observable outcomes that depend on the use
of assets.  The definitions seek to avoid describing internal details of
implementations or their architecture.


## AI Model Training {#train-ai}

Using an asset to modify the learned parameters of an AI model
that is used
to generate synthetic content in one or more modalities.

## Search {#search}

Use of an asset in an application
where the primary purpose of the application
is to select assets
and direct users to the location of those assets.

This category of use only applies under the following conditions:

* Where the presentation of an asset in search output --
  if selected for presentation --
  includes a direct reference or link
  to the original location from which the asset was retrieved.

* When excerpts from the asset are displayed
  they serve to assist users
  in evaluating the relevance of the result.

This category does not include the use of assets
to generate summaries.

Non-substantive changes to the presentation
of titles or excerpts from assets
are included for the purposes of accessibility.
Translation, transcription, or text-to-speech
are examples of non-substantive changes
that could help users understand what is being presented.
Where existing controls restrict presentation of these items,
such as limitations on snippet size,
those apply before any changes.

A preference to allow this category of use
includes allowing any processing internal to the application
that is performed on assets.
Allowing this use is conditional on the outputs of any processing
being exclusively used by the search application
according to the other restrictions in this section.
That includes the training of AI models
using the assets
and the use of those models
provided that the resulting models
and their outputs
are used exclusively
in ways that meet the above conditions.


## Inference Directed by User {#ai-inference-user}

Using an asset during inference where the human user, on whose behalf the inference is performed, has provided the asset itself, or a reference to the asset that the system retrieves directly without performing any selection step.


## Inference Directed by System {#ai-inference-system}

Using an asset during inference in any other circumstance, including where the asset is identified through one or more selection steps.
Where a system begins from a user-provided reference and retrieves further assets of its own choosing, those further assets are selected by the system.

This category applies at the time of use, regardless of when or how the asset was acquired.


## Vocabulary Extensions {#vocab-extension}

Extensions to this vocabulary are defined
in a standards-track RFC that updates this document.

The definition of the extension MUST define
how any potential overlap between usage categories is resolved.
Definitions can identify which usage category applies
for any such overlap.

Systems that use this vocabulary might seek to integrate
the terms in this vocabulary
as part of a larger data model that includes other terms not defined here.
Such usage is not subject to the RFC requirement above,
but special care is needed
to avoid defining overlapping categories of use.
{{mapping}} describes how concepts from an alternative format
might be mapped to this vocabulary.


# Applying Statements of Preference {#usage}

After acquiring a statement of preference,
which might use the process in {{processing}},
an application can determine the status of a specific usage category.

If the statement of preference contains an explicit preference
regarding that category of use --
either to allow or disallow --
that is the outcome.
Otherwise, the preference for that category is unknown.

This process results in one of three potential answers:
allow, disallow, and unknown.
Applications can use the answer to guide their behavior.

One approach for dealing with an unknown outcome
is to assign a default value.
This document takes no position on what default might be assigned.


## Combining Preferences {#combine}

An application might receive multiple statements of preference,
obtained using different methods
or from different declaring parties.
This might result in conflicting preferences.

Absent some other means of resolving conflicts,
the following process applies to each usage category:

* If any statement of preference indicates that the usage is disallowed,
  the result is that the usage is disallowed.

* Otherwise, if any statement of preference allows the usage,
  the result is that the usage is allowed.

* Otherwise, the preference for that category is unknown.

This process ensures that the most restrictive preference applies.


## More Specific Instructions

A recipient of a statement of preferences
that follows the model in {{model}}
might receive more specific instructions in two ways:

* Extensions to the vocabulary
  might add qualifications or conditions to preferences about usage.

* Contractual agreements or other specific arrangements might override
  statements of preference.

For instance, a statement of preferences might indicate a preference
to disallow a category of use for an asset.
If arrangements, such as legal agreements, exist that explicitly permit the use of that asset,
those arrangements likely apply despite the existence of machine-readable statements of preference,
unless the terms of the arrangement explicitly say otherwise.


# Exemplary Serialization Format {#format}

This section defines an exemplary serialization format for preferences.
The format describes how the abstract model could be turned into Unicode text or sequence of bytes.

The format relies on the Dictionary type defined in {{Section 3.2 of FIELDS}}.
The dictionary keys correspond to usage categories
and the dictionary values correspond to explicit preferences,
which can be either `y` or `n`; see {{y-or-n}}.

For example, the following states a preference
to allow model training ({{train-ai}}),
disallow search ({{search}}),
with the preference for other categories being unknown:

~~~
train-ai=y, search=n
~~~


## Usage Category Labels {#labels}

Each usage category in the vocabulary ({{vocab}}) is mapped to a short textual label.
{{t-category-labels}} tabulates this mapping.

| Category                    | Label        | Reference               |
|:----------------------------|:-------------|:------------------------|
| AI Model Training           | train-ai     | {{train-ai}}            |
| Search                      | search       | {{search}}              |
| Inference Directed by User  | infer-user   | {{ai-inference-user}}   |
| Inference Directed by System| infer-system | {{ai-inference-system}} |
{: #t-category-labels title="Mappings for Categories"}

These tokens are case sensitive.

Tokens defined for a new usage category can only use
lowercase latin characters (a-z), digits (0-9), "_", "-", ".", or "*".
These are encoded using the mappings in {{ASCII}}.


## Preference Labels {#y-or-n}

The data model in {{model}} used has two options for explicit preferences
associated with each category: allow and disallow.
These are mapped to single byte Tokens ({{Section 3.3.4 of FIELDS}})
of `y` and `n`, respectively.


## Text Encoding

Structured Fields {{FIELDS}} describes a byte-level encoding of information,
not a text encoding.
This makes this format suitable for inclusion in any protocol or format that carries bytes.

Some formats are defined in terms of strings rather than bytes.
These formats might need to decode the bytes of this format to obtain a string.
As the syntax is limited to ASCII {{ASCII}},
an ASCII or UTF-8 decoder {{UTF8}} can be used.
This results in the strings that this document uses.

Processing (see {{processing}}) requires a sequence of bytes,
so any format that uses strings needs to encode strings first.
Again, this process can use ASCII or UTF-8.


## Syntax Extensions {#extension}

There are two ways by which this syntax might be extended:
the addition of new labels and the addition of parameters.

New labels might be defined to correspond to new usage categories.
{{vocab-extension}} addresses the considerations for defining new categories.

New labels might also be defined for other types of extension
that do not assign a preference to a usage category.
In either case, when processing a parsed Dictionary to obtain preferences,
any unknown labels MUST be ignored.

The Dictionary syntax ({{Section 3.2 of FIELDS}}) can associate parameters
with each key-value pair.
This document does not define any semantics for any parameters that might be included.
When processing a parsed Dictionary to obtain preferences,
any unknown parameters MUST be ignored.

In either case,
new extensions need to be defined in an RFC that updates this document.


## Processing Algorithm {#processing}

To process a series of bytes to recover the stated preferences,
those bytes are parsed into a Dictionary ({{Section 4.2.2 of FIELDS}}),
then preferences are assigned to each usage category in the vocabulary.

This algorithm produces a keyed collection of values,
where each key has at most one value and optional parameters.

To obtain preferences,
iterate through the defined categories in the vocabulary.
For the label that corresponds to that category (see {{t-category-labels}}),
obtain the corresponding value from the collection,
disregarding any parameters.
A preference is assigned as follows:

* If the value is a Token with a value of `y`,
  the associated preference is to allow that category of use.

* If the value is a Token with a value of `n`,
  the associated preference is to disallow that category of use.

* Otherwise, the preference for that category is unknown.

Note that this last alternative includes
the key being absent from the collection,
values that are not Tokens,
and Token values that are other than `y` or `n`.
All of these are not errors,
they only result in the corresponding preference being unknown.

This process results in an abstract data model
that assigns a preference to each usage category
as described in {{model}}.


### Multiple Preferences

It is important to note that
if the same key appears multiple times,
the algorithms in {{FIELDS}} ensure that only the last value applies.
This means that duplicating a key could result in unexpected outcomes.
For example, the following results in all preferences being unknown,
because the type of the parameter values
(a boolean and a string respectively)
are not tokens:

~~~
train-ai=y, train-ai, search=n, search="n"
~~~

If the parsing of the Dictionary fails, preferences are unknown.
This includes where keys include uppercase characters,
as this format is case sensitive
(more correctly, it operates on bytes, not strings).

### Preference Parameters

This document does not define a use for parameters.
Only those parameters associated with the value that is selected
according to {{Section 4.2.2 of FIELDS}} apply.
For example, the following preference carries no parameters,
and a preference to allow the usage:

~~~
train-ai;allow=n, train-ai=y
~~~

Parameters can therefore be carried for any preference value,
including where preferences are unknown.
For example, the following `train-ai` preference has parameters
even though the preference is unknown:

~~~
train-ai;has;parameters="?";
~~~


## Alternative Formats {#mapping}

The format defined in this document
is only an exemplary way to represent preferences.
The data model described in {{model}}
can be used without this serialization.

Any alternative format needs to define the mapping
both from that format to the model used in this document
and from the model to the alternative format.
This includes any potential for extensions ({{extension}}).

The mapping between the data model and the alternative format
does not need to be complete,
it only needs to be clear and unambiguous.

For example, an alternative format
might only provide the ability to convey preferences
for a subset of the categories of use.
A mapping might then define that an unknown preference
is associated with other categories.


# Security Considerations

Preferences are not a security mechanism.
{{applicability}} addresses what it means to express a preference.

Processing a concrete instantiation
of the exemplary format described in {{format}}
is subject to the security considerations in {{Section 6 of FIELDS}}.


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

The following individuals made significant contributions to this document:

* {{{Cullen Miller}}}
* {{{Erin Simon}}}
* {{{Felix Reda}}}
* {{{Kevin Kelley}}}
* {{{Krishna Madhavan}}}
* {{{Laurent Le Meur}}}
* {{{Leonard Rosenthol}}}
* {{{Lila Bailey}}}
* {{{Nate Hake}}}
* {{{Sebastian Posth}}}
* {{{Timid Robot Zehta}}}
{: spacing="compact"}
