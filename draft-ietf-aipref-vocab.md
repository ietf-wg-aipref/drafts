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
  EUCD2019:
    title: "Directive (EU) 2019/790 of the European Parliament and of the Council of 17 April 2019 on copyright and related rights in the Digital Single Market"
    target: https://eur-lex.europa.eu/eli/dir/2019/790/oj
    author:
      - org: European Union
    date: 2019-05-17
  UTF8: RFC3629

...

--- abstract

This document proposes a standardized vocabulary for expressing preferences
related to how digital assets are used by automated processing systems.
This vocabulary allows for the creation of structured declarations
about restrictions or permissions for use of digital assets by such systems.


--- middle

# Introduction

This document defines a common vocabulary of terms for automated systems that process digital assets.
The primary purpose of this vocabulary is to enable machine-readable expressions of preferences
about how digital assets are used by automated processing systems
in the context of training AI models and other forms of automated processing.

The terms defined by the vocabulary can be used to describe,
in a standardized way,
the types of uses that a declaring party may wish to explicitly restrict or allow.
Preferences are then expressed as a grant or denial of permission
concerning each of the types of use defined in the vocabulary.
This ensures that preferences can be communicated, processed, and stored in a consistent and interoperable manner.

The vocabulary or the preferences that might be expressed
do not proscribe how automated processing systems obtain or act on preferences.
Separate documents will describe how preferences might be associated with assets.
It is designed to ensure that preference information can be exchanged between different systems
and consistently understood.


The vocabulary is intended to be usable
both where expressing preferences results in legal obligations
and where there are no associated legal protections.
That is, preferences can be expressed to invoke specific protections,
or they can be made without any presumption of specific legal consequences.
Potential legal obligations include rights reservations made by rightholders
in jurisdictions with conditional exceptions on copyright protections.
Expressing preferences is without prejudice to applicable laws,
including the applicability of exceptions and limitations to copyright.


# Conventions and Definitions

{::boilerplate bcp14-tagged}

This document uses the following terms:

{: newline="true" spacing="compact"}
Asset:
: A digital file or stream of data, usually with associated metadata.

Declaring party:
: The entity that expresses a preference with regards to an Asset.

# Statements of Preference {#model}

The vocabulary is a set of categories,
each of which is defined to cover a class of usage for assets.
{{vocab}} defines these categories in more detail.

A statement of preference is made about an asset.
Statements of preferences can assign preferences
to each of the categories of use in the vocabulary.
Preferences regarding each category can be expressed
either to allow or disallow the usage associated with the category.

A statement of preferences can express preferences
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
the recipient can assume that each category of use has a preference
in one of three states: "allowed", "disallowed", or "unknown".


# Vocabulary Definition {#vocab}

This section defines the categories of use in the vocabulary.

{{f-categories}} shows the relationship between these categories:

~~~ aasvg
 .-------------------------------------------------.
|                                                   |
|               Automated Processing                |
|                                                   |
|   .-------------------------------------------.   |
|  |                .------------------------.   |  |
|  |               |                          |  |  |
|  |               |                          |  |  |
|  |  AI Training  |  Generative AI Training  |  |  |
|  |               |                          |  |  |
|  |               |                          |  |  |
|  |                '------------------------'   |  |
|   '-------------------------------------------'   |
|                                                   |
|    .----------------.       .----------------.    |
|   |                  |     |                  |   |
|   |                  |     |                  |   |
|   |      AI Use      |     |      Search      |   |
|   |                  |     |                  |   |
|   |                  |     |                  |   |
|    '----------------'       '----------------'    |
|                                                   |
 '-------------------------------------------------'
~~~
{: #f-categories title="Relationship Between Categories of Use"}


## Automated Processing Category {#all}

The act of using one or more assets in the context of automated processing aimed at analyzing text and data in order to generate information which includes but is not limited to patterns, trends and correlations.

The use of assets for automated processing encompasses all the subsequent categories.

## AI Training Category {#train-ai}

The act of training machine learning models or artificial intelligence (AI).

The use of assets for AI Training is a proper subset of Automated Processing usage.

## Generative AI Training Category {#train-genai}

The act of training general purpose AI models that have the capacity to generate text, images or other forms of synthetic content, or the act of training more specialized AI models that have the purpose of generating text, images or other forms of synthetic content.

The use of assets for Generative AI Training is a proper subset of AI Training usage.


## AI Use Category {#ai-use}

The act of using one or more assets as input to a trained AI/ML model as part of the operation of that model (as opposed to the training of the model).

The use of assets for AI Training is a proper subset of Automated Processing usage.


## Search Category {#search}

Using one or more assets in a search application that directs users to the location from which the assets were retrieved.

The purpose of defining a distinct Search
category is to allow preferences to be expressed about search applications,
independent of other categories of use.
A distinct Search category allows for preferences specific to search applications,
even if the use of AI is involved in their implementation.

The use of assets for Search is a proper subset of Automated Processing usage.


# Usage

The vocabulary is used by referencing the terms defined in {{vocab}},
directly or via mappings,
in accordance with how they are defined in this document.


## More Specific Instructions

A recipient of a statement of preferences that follows this model might receive more specific instructions
in two ways:

* Extensions to the vocabulary might define more specific categories of usage.
  Preferences about more specific categories override those of any more general category.

* Statements of preferences are general purpose, machine-readable statements
  that cannot override contractual agreements or more specific statements.

For instance, a statement of preferences might indicate that the use of an asset is disallowed for AI Training.
If arrangements, such as legal agreements, exist that explicitly permit the use of that asset, those arrangements likely apply, unless the terms of the arrangement explicitly say otherwise.


## Vocabulary Extensions {#vocab-extension}

Systems referencing the vocabulary MUST NOT introduce additional categories that include existing categories defined in the vocabulary or otherwise include additional hierarchical relationships.


# Exemplary Serialization Format {#format}

This section defines an exemplary serialization format for preferences.
The format describes how the abstract model could be turned into Unicode text or sequence of bytes.

The format relies on the Dictionary type defined in {{Section 3.2 of FIELDS}}.
The dictionary keys correspond to usage categories
and the dictionary values correspond to explicit preferences,
which can be either `y` or `n`; see {{y-or-n}}.

For example, the following is a preference to allow AI training ({{train-ai}}),
disallow generative AI training ({{train-genai}}), and
and state no preference for other categories other than subsets of these categories:

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
| AI Use                 | ai-use      | {{ai-use}}      |
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


# Consulting a Preference Expression {#consulting}

After processing a preference expression ({{processing}}),
an application can request the status of a specific usage category.

A single preference expression can be evaluated for a usage category
as follows:

1. If the expression contains an explicit preference
   (either to allow or disallow),
   that is the result.

2. Otherwise, if the usage category is a proper subset
   of another usage category,
   recursively apply this process to that category
   and use the result of that process.

3. Otherwise, no preference is expressed.

This process results in three potential answers:
allow, disallow, and no preference.
Applications can use the answer to guide their behavior.

One approach for dealing with a "no preference" answer
is to assign a default.
This document takes no position on what default might be chosen
as that will depend on policy constraints
beyond the scope of this specification.


## Combining Preferences {#combining}

The application might have multiple preference expressions,
obtained using different methods.

If multiple preference expressions are active,
all preference expressions are consulted ({{consulting}}).
This might result in conflicting answers.

Absent some other means of resolving conflicts,
the following process applies to each usage category:

* If any preference expression indicates that the usage is disallowed,
  the result is that the usage is disallowed.

* Otherwise, if any preference preference allows the usage,
  the result is that the usage is allowed.

* Otherwise, no preference is expressed.

This process ensures that the most restrictive preference applies.


# Applicability and Legal Effect

This document provides a set of definitions for different categories of use,
plus a system for associating simple preferences to each
(allow, disallow, or no preference).

The categories of use that are defined as part of the vocabulary
are not always clearly applicable or inapplicable to a particular system or application.
The universe of possible systems is far more complex
than any simple vocabulary is capable of describing.
That means that some discretion could be involved
in deciding whether a preference applies.

The expression of preferences might activate regulatory or legal consequences,
which has implications for entities that consume those preferences.
Their interpretation of the meaning of different terms
could have legal ramifications.
Different jurisdictions could reach subtly different conclusions
about the precise scope of each category of use
that affects the applicability of each.

It is the responsibility of those that process affected assets to understand
the legal implications of their use of digital assets.

This includes understanding:

* obligations regarding how preferences are obtained
  (in particular, which methods of associating preferences with content
  are expected to be understood),

* the specific uses to which assets are put,

* how preferences apply to the those uses, and

* how relevant jurisdictions might interpret those preferences.

These considerations will depend on jurisdiction
and the details of the system.


# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

The following individuals made significant contributions to this document:

* {{{Cullen Miller}}}
* {{{Laurent Le Meur}}}
* {{{Leonard Rosenthol}}}
* {{{Sebastian Posth}}}
* {{{Timid Robot Zehta}}}
{: spacing="compact"}
