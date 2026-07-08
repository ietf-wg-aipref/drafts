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

Some categories describe a proper subset of the usages of other categories.
A preference that is stated for the more general category applies
if the preference is unknown for the more specific category.

For example, a more general category might be assigned a preference
that allows the associated usage.
In the absence of any statement of preference
regarding categories that are more specific subsets of that usage category,
usage within those categories would be also be allowed.
An explicit preference regarding the more specific usage category
can be used to disallow the more specific usage,
while indicating that other usage within the more general category
is permissible.

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

- provide for enforcement;
- address if, how, or when preferences should be followed or ignored;
- address technical, legal, contractual, or other mechanisms that might create
  a stronger requirement to follow or not follow preferences;
- consider situations or purposes that might justify following or ignoring
  expressed preferences.

Because of this, stakeholders need to evaluate when and how to respect
preferences in the context of other legal, institutional, or ethical
commitments.


# Vocabulary Definition {#vocab}

NOTE: This section does not yet have consensus; see "Note to Readers" above.

This section defines the categories of use in the vocabulary.

These categories describe concrete, observable outcomes that depend on the use
of assets.  The definitions seek to avoid describing internal details of
implementations or their architecture.


## AI Model Training {#train-ai}

The act of using an asset
in the production or refinement of an AI model
that can generate content in one or more modalities
(text, image, audio, etc...).


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


## Vocabulary Extensions {#vocab-extension}

Extensions to this vocabulary need to be defined in an RFC
that updates this document.

Any future extensions to this vocabulary MUST NOT introduce additional categories
that include existing categories defined in the vocabulary.
That is, new categories of use can be defined as a subset of an existing category,
but not a superset.

Systems that use this vocabulary might define their own extensions
as part of a larger data model.
{{mapping}} describes how concepts from an alternative format
might be mapped to this vocabulary.


# Applying Statements of Preference {#usage}

After acquiring a statement of preference,
which might use the process in {{processing}},
an application can determine the status of a specific usage category
as follows:

1. If the statement of preference contains an explicit preference
   regarding that category of use --
   either to allow or disallow --
   that is the result.

2. Otherwise, if the usage category is a proper subset
   of another usage category,
   recursively apply this process to that category
   and use the result of that process.

3. Otherwise, the preference for that category is unknown.

This process results in one of three potential answers:
allow, disallow, and unknown.
Applications can use the answer to guide their behavior.

One approach for dealing with an "unknown" outcome
is to assign a default value.
This document takes no position on what default might be assigned.


## Combining Preferences {#combine}

an application might receive multiple statements of preference,
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

* Extensions to the vocabulary might define more specific categories of usage.
  Preferences about more specific categories override those of any more general category.

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
and preference for other categories
other than subsets of these categories are unknown:

~~~
train-ai=y, search=n
~~~


## Usage Category Labels {#labels}

Each usage category in the vocabulary ({{vocab}}) is mapped to a short textual label.
{{t-category-labels}} tabulates this mapping.

| Category                    | Label       | Reference       |
|:----------------------------|:------------|:----------------|
| AI Model Training           | train-ai    | {{train-ai}}    |
| Search                      | search      | {{search}}      |
{: #t-category-labels title="Mappings for Categories"}

These tokens are case sensitive.

Tokens defined for a new usage category can only use
lowercase latin characters (a-z), digits (0-9), "_", "-", ".", or "*".
These are encoded using the mappings in {{ASCII}}.


## Preference Labels {#y-or-n}

The data model in {{model}} used has two options for preferences
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
an ASCII decoder or UTF-8 decoder {{UTF8}} can be used.
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

It is important to note that
if the same key appears multiple times,
only the last value is taken.
This means that duplicating a key could result in unexpected outcomes.
For example, the following means that preferences are unknown,
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

This document does not define a use for parameters.
Where parameters are used,
only those parameters associated with the value that is selected
according to {{Section 4.2.2 of FIELDS}}.
Parameters can therefore be carried for any preference value,
including where preferences are unknown.

For example, the following `train-ai` preference has parameters
even though the preference is unknown:

~~~
train-ai;has;parameters="?";
~~~

This process produces an abstract data model
that assigns a preference to each usage category
as described in {{model}}.


## Alternative Formats {#mapping}

This format is only an exemplary way to represent preferences.
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
* {{{Krishna Madhavan}}}
* {{{Laurent Le Meur}}}
* {{{Leonard Rosenthol}}}
* {{{Lila Bailey}}}
* {{{Nate Hake}}}
* {{{Sebastian Posth}}}
* {{{Timid Robot Zehta}}}
{: spacing="compact"}
