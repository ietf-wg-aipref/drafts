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
Artificial Intelligence (AI):
: An engineered system of sufficient complexity that,
  for a given set of human-defined objectives,
  learns from data to generate outputs
  such as content, predictions, recommendations, or decisions.

AI Training:
: The application of machine learning to data
  to produce or improve a model for an artificial intelligence system.

Asset:
: A digital file or stream of data, usually with associated metadata.

Declaring party:
: The entity that expresses a preference with regards to an Asset.

Machine Learning (ML):
: The processing of data
  to produce or improve a model that encodes the relationship
  between the data and human-defined objectives.

Search Application:
: A search application is a system that enables users
  locate items on the internet or in a specific data store.


# Statements of Preference {#model}

The vocabulary is a set of categories,
each of which is defined to cover a class of usage for assets.
{{vocab}} defines the core set of usage categories in detail.

A statement of preference -- or usage preference -- is made about an asset.
Statements of preferences can assign a preference
to each of the categories of use in the vocabulary.
Preferences regarding each category can be expressed
either to allow or disallow the usage associated with the category.

A statement of preference can express a preference
about some, all, or none of the categories from the vocabulary.
This can mean that no preference is expressed for a given usage category.

Some categories describe a proper subset of the usages of other categories.
A preference that is expressed for the more general category applies
if no preference is expressed for the more specific category.

For example, the Automated Processing category might be assigned a preference that allows the associated usage.
In the absence of any statement of preference regarding the AI Training category,
that usage would be also be allowed,
as AI Training is a subset of the Automated Processing category.
In comparison, an explicit preference regarding AI Training might disallow that usage,
while permitting other usage within the Automated Processing category.

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


## Conformance

This document and {{ATTACH}}
describe how statements of preference are associated with assets.
An implementation is conformant to these specifications
if it correctly follows all normative requirements that apply to it.

The process of obtaining a statement of preference has very limited scope
for variation between implementations.

## Applicability and Effect {#applicability}

This specification provides a set of definitions for different
categories of use, plus a system for associating simple
preferences to each (allow, disallow, or no preference; see {{model}}).

This specification does not provide any enforcement mechanism
for those preferences, and conformance to it does not encompass
whether preferences are actually respected during data processing.

Preferences do not themselves create rights or prohibitions,
either in the positive or the negative. Other mechanisms—technical,
legal, contractual, or otherwise—might enforce stated preferences
and thereby determine the consequences of following or not following
a stated preference.

An entity that receives usage preferences MAY choose to respect
those preferences it has discovered, according to
an understanding of how the asset is used,
how that usage corresponds to the usage categories
where preferences have been expressed,
and the applicable legal context.

Usage preferences can be overridden through express agreements
between relevant parties, by explicit provisions of law, or
through the exercise of discretion in situations where other
priorities justify doing so. Priorities that could justify
ignoring preferences include—but are not limited to—free
expression, safety, education, scholarship, research,
preservation, interoperability, and accessibility.

The following lists examples of cases
where other priorities could override specific preferences:

* People with accessibility needs,
  or organizations working on their behalf,
  might ignore a preference to disallow Automated Processing ({{all}})
  in order to access automated captions
  or generate accessible formats.

* A cultural heritage organization could ignore a preference
  to disallow Automated Processing ({{all}})
  in order to provide more useful, reliable, or discoverable access
  to historical web collections.

* An educational institution could ignore a preference
  to disallow AI Training ({{train-ai}})
  in order to enable scholars to develop or use tools
  to facilitate scientific or other types of research.

* A website that permits user uploads could ignore a preference
  to disallow Automated Processing ({{all}})
  in order to develop or use tools that detect harmful content
  according to established terms of use.

Because enforcement is not provided by this specification,
the consequences of ignoring preferences could vary
depending upon how a given legal jurisdiction recognizes preferences.

# Vocabulary Definition {#vocab}

This section defines the categories of use in the vocabulary.

{{f-categories}} shows the relationship between these categories:

~~~ aasvg
 .---------------------------------------------.
|                                               |
|             Automated Processing              |
|                                               |
|                                               |
|    .-----------------.      .------------.    |
|   |                   |    |              |   |
|   |                   |    |              |   |
|   |    AI Training    |    |    Search    |   |
|   |                   |    |              |   |
|   |                   |    |              |   |
|   |  .-------------.  |     '------------'    |
|   | |               | |                       |
|   | |  Generative   | |                       |
|   | |  AI Training  | |                       |
|   | |               | |                       |
|   |  '-------------'  |                       |
|   |                   |                       |
|    '-----------------'                        |
|                                               |
 '---------------------------------------------'
~~~
{: #f-categories title="Relationship Between Categories of Use"}


## Automated Processing Category {#all}

The act of using automated processing on one or more assets
to analyze text and data in order to generate information
which includes but is not limited to patterns, trends and correlations.

The use of assets for automated processing encompasses all the subsequent categories.

## AI Training Category {#train-ai}

The act of training machine learning models or artificial intelligence (AI).

The use of assets for AI Training is a proper subset of Automated Processing usage.

## Generative AI Training Category {#train-genai}

The act of training general purpose AI models that have the capacity to generate text, images or other forms of synthetic content, or the act of training more specialized AI models that have the purpose of generating text, images or other forms of synthetic content.

The use of assets for Generative AI Training is a proper subset of AI Training usage.


## Search Category {#search}

Using one or more assets in a search application
that directs users to the location
from which the assets were retrieved.

Search applications can be complex
and may serve multiple purposes.
Only those parts of applications that direct users to the location of an asset
are included in this category of use.
This includes the use of titles or excerpts from assets
that are used to help users select between multiple candidate options.

Preferences for the Search category apply to those parts of applications
that provide search capabilities,
regardless of what other preferences are expressed.

Parts of applications that do not direct users to the location of assets,
such as summaries,
are not covered by this category of use.

The use of assets for Search is a proper subset of Automated Processing usage.


## Vocabulary Extensions {#vocab-extension}

Systems referencing the vocabulary MUST NOT introduce additional categories
that include existing categories defined in the vocabulary.
That is, new categories of use can be defined as a subset of an existing category,
but not a superset.


# Applying Statements of Preference {#usage}

After acquiring a statement of preference,
which might use the process in {{processing}},
an application can request the status of a specific usage category.

A statement of preference can be evaluated
for a single usage category
as follows:

1. If the expression contains an explicit preference
   regarding that category of use --
   either to allow or disallow --
   that is the result.

2. Otherwise, if the usage category is a proper subset
   of another usage category,
   recursively apply this process to that category
   and use the result of that process.

3. Otherwise, no preference is expressed.

This process results in one of three potential answers:
allow, disallow, and unknown.
Applications can use the answer to guide their behavior.

One approach for dealing with an "unknown" outcome
is to assign a default value.
This document takes no position on what default might be assigned.


## Combining Preferences {#combine}

The application might have multiple statements of preference,
obtained using different methods
or from different declaring parties.
This might result in conflicting answers.

Absent some other means of resolving conflicts,
the following process applies to each usage category:

* If any preference expression indicates that the usage is disallowed,
  the result is that the usage is disallowed.

* Otherwise, if any preference preference allows the usage,
  the result is that the usage is allowed.

* Otherwise, no preference is expressed.

This process ensures that the most restrictive preference applies.


## More Specific Instructions

A recipient of a statement of preferences that follows this model might receive more specific instructions
in two ways:

* Extensions to the vocabulary might define more specific categories of usage.
  Preferences about more specific categories override those of any more general category.

* Statements of preferences are general purpose, machine-readable statements
  that cannot override contractual agreements or more specific statements.

For instance, a statement of preferences might indicate that the use of an asset is disallowed for AI Training.
If arrangements, such as legal agreements, exist that explicitly permit the use of that asset, those arrangements likely apply, unless the terms of the arrangement explicitly say otherwise.


# Exemplary Serialization Format {#format}

This section defines an exemplary serialization format for preferences.
The format describes how the abstract model could be turned into Unicode text or sequence of bytes.

The format relies on the Dictionary type defined in {{Section 3.2 of FIELDS}}.
The dictionary keys correspond to usage categories
and the dictionary values correspond to explicit preferences,
which can be either `y` or `n`; see {{y-or-n}}.

For example, the following expresses a preference to allow AI training ({{train-ai}}),
disallow generative AI training ({{train-genai}}), and
and expresses no preference for other categories
other than subsets of these categories:

~~~
train-ai=y, train-genai=n
~~~


## Usage Category Labels {#labels}

Each usage category in the vocabulary ({{vocab}}) is mapped to a short textual label.
{{t-category-labels}} tabulates this mapping.

| Category               | Label       | Reference       |
|:-----------------------|:------------|:----------------|
| Automated Processing   | all         | {{all}}         |
| AI Training            | train-ai    | {{train-ai}}    |
| Generative AI Training | train-genai | {{train-genai}} |
| Search                 | search      | {{search}}      |
{: #t-category-labels title="Mappings for Categories"}

Any mapping for a new usage category can only use
lowercase latin characters (a-z), digits (0-9), "_", "-", ".", or "*".
These are encoded using the mappings in {{ASCII}}.


## Preference Labels {#y-or-n}

The abstract model used has two options for preferences associated with each category:
allow and disallow.
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

In either case, new extensions need to be defined in an RFC that updates this document.


## Processing Algorithm {#processing}

To process a series of bytes to recover the expressed preferences,
those bytes are parsed into a Dictionary ({{Section 4.2.2 of FIELDS}}),
then preferences are assigned to each usage category in the vocabulary.

The parsing algorithm for a Dictionary
produces a keyed collection of values,
each with a possibly-empty set of parameters.
The parsing process guarantees that each key has at most one value and parameters.

To obtain preferences for each of the categories in the vocabulary,
iterate through the categories.
For the label that corresponds to that category (see {{t-category-labels}}),
obtain the corresponding value from the collection,
disregarding any parameters.
A preference is assigned as follows:

* If the value is a Token with a value of `y`,
  the associated preference is to allow that category of use.

* If the value is a Token with a value of `n`,
  the associated preference is to disallow that category of use.

* Otherwise, a preference is not expressed for that category of use.

Note that this last alternative includes
the key being absent from the collection,
values that are not Tokens,
and Token values that are other than `y` or `n`.
All of these are not errors,
they only result in no preference being inferred.

An important note about this process and format is that,
if the same key appears multiple times,
only the last value is taken.
This means that duplicating the same key could result in unexpected outcomes.
For example, the following expresses no preferences:

~~~
train-ai=y, train-ai="n", train-genai=n, train-genai, all=n, all=()
~~~

If the parsing of the Dictionary fails, no preferences are expressed.
This includes where keys include uppercase characters,
as this format is case sensitive
(more correctly, it operates on bytes, not strings).

This process produces an abstract data structure
that assigns a preference to each usage category
as described in {{model}}.


## Alternative Formats

This format is only an exemplary way to represent preferences.
The model described in {{model}}, can be used without this serialization.

Any alternative format needs to define the mapping
both from that format to the model used in this document
and from the model to the alternative format.
This includes any potential for extensions ({{extension}}).

The mapping between the model and the alternative format
does not need to be complete,
it only needs to be clear and unambiguous.

For example, an alternative format
might only provide the ability to convey preferences
for a subset of the categories of use.
A mapping might then define that no preference is associated with other categories.


# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

The following individuals made significant contributions to this document:

* {{{Lila Bailey}}}
* {{{Cullen Miller}}}
* {{{Laurent Le Meur}}}
* {{{Leonard Rosenthol}}}
* {{{Sebastian Posth}}}
* {{{Timid Robot Zehta}}}
{: spacing="compact"}
