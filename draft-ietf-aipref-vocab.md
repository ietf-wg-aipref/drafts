---
title: "A Vocabulary For Expressing AI Usage Preferences"
abbrev: "AI Preferences"
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

informative:
 EUCD2019:
    title: "Directive (EU) 2019/790 of the European Parliament and of the Council of 17 April 2019 on copyright and related rights in the Digital Single Market"
    target: https://eur-lex.europa.eu/eli/dir/2019/790/oj
    author:
     org: European Union
    date: 2019-05-17

--- abstract

This document proposes a standardized vocabulary for expressing preferences related to how digital assets are used by automated processing systems.
This vocabulary allows for the creation of structured declarations about restrictions or permissions for use of digital assets by such systems.
The vocabulary is agnostic to the means by which it is conveyed.
The definitions in the vocabulary facilitate a shared understanding between entities that express such preferences and those that might use the associated digital assets.

--- middle

# Introduction

This document defines a common vocabulary of terms for automated systems that process digital assets. The primary purpose of this vocabulary is to enable machine-readable expressions of preferences about how digital assets are used by automated processing systems, in the context of training AI models and other forms of text and data mining (TDM).

The terms defined by the vocabulary can be used to describe, in a standardized way, the types of uses that a declaring party may wish to explicitly restrict or allow.
Preferences are then expressed as a grant or denial of permission concerning each of the types of use defined in the vocabulary.
This ensures that preferences can be communicated, processed, and stored in a consistent and interoperable manner.

The vocabulary is neutral to the technical details of how systems act on preferences.
It is designed to ensure that preference information can be exchanged between different systems and consistently understood.

The vocabulary is intended to govern the use of digital assets for the training of AI models and other forms of automated processing.
It does not concern itself with the mechanisms involved in obtaining digital assets (i.e., crawling).

The vocabulary is intended to work in contexts where such preferences result in legal obligations (such as rights reservations made by rightholders in jurisdictions with conditional TDM exceptions), and in contexts where this is not the case. It is without prejudice to applicable laws and the applicability of exceptions and limitations to copyright.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

This document uses the following terms:

{: newline="true" spacing="compact"}
Asset:
: A digital file or stream of data, usually with associated metadata.

Declaring party:
: The entity that expresses a preference with regards to an Asset.

# Statements of Preference

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

For example, the TDM category might be assigned a preference that allows the associated usage.
In the absence of any statement of preference regarding the AI Training category,
that usage would be also be allowed,
as AI Training is a subset of the TDM category.
In comparison, an explicit preference regarding AI Training might disallow that usage,
while permitting other usage within the TDM category.

After processing a statement of preferences
the recipient can assume that each category of use has a preference
in one of three states: "allowed", "disallowed", or "unknown".


# Vocabulary Definition {#vocab}

This section defines the categories of use in the vocabulary.

The figure below shows the relationship between these categories:

<figure>
<name>NMS View of Device State</name>
<artset>
<artwork type="svg">
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1" width="750" height="300" viewBox="0 0 750 300" text-anchor="middle" stroke-width="2" font-size="14" font-family="sans-serif">
  <title>Opt-out vocabulary overview</title>

  <!-- Outer Box -->
  <rect x="50" y="50" width="630" height="220" rx="15" ry="15" fill="white" stroke="black"/>
  <text x="365" y="80">Text and Data Mining</text>

  <!-- AI Training Box -->
  <rect x="80" y="100" width="380" height="130" rx="10" ry="10" fill="white" stroke="black" />
  <text x="380" y="170">AI Training</text>

  <!-- Generative AI Training Box -->
  <rect x="110" y="130" width="200" height="70" rx="5" ry="5" fill="white" stroke="black" />
  <text x="210" y="170">Generative AI Training</text>

  <!-- Additional Use Cases Box -->
  <rect x="500" y="100" width="150" height="130" class="dotted" rx="5" ry="5" fill="white" stroke="black" stroke-dasharray="5,5" />
  <text x="575" y="160">[possibly]:</text>
  <text x="575" y="180">additional use cases</text>
</svg>

</artwork>
<artwork type="ascii-art">
+--------------------------------------------------------------------------+
|                                                                          |
|                          Text and Data Mining (TDM)                      |
|                                                                          |
| +--------------------------------------------+  +- - - - - - - - - - -+  |
| |  +--------------------------+              |  |                     |  |
| |  |                          |              |                           |
| |  |                          |              |  |    [possibly]:      |  |
| |  | Generative AI Training   |  AI Training |                           |
| |  |                          |              |  |  Other use cases    |  |
| |  |                          |              |                           |
| |  +--------------------------+              |  |                     |  |
| +--------------------------------------------+  +- - - - - - - - - - -+  |
|                                                                          |
+--------------------------------------------------------------------------+
</artwork>
</artset>
</figure>

This list of specific use cases may be expanded in the future, should a consensus emerge between stakeholders, to include categories that address additional use cases as they emerge. In addition to these categories defined in the vocabulary, it is also expected that some systems implementing this vocabulary may extend this list with additional categories for their particular needs.

## Text and Data Mining (TDM) Category

The act of using one or more assets in the context of any automated analytical technique aimed at analyzing text and data in digital form in order to generate information which includes but is not limited to patterns, trends and correlations.

The overarching TDM category is based on the definition of Text and Data Mining in Article 2(2) of {{EUCD2019}}.

## AI Training Category

The act of training machine learning models or artificial intelligence (AI).

The use of assets for AI Training is a proper subset of TDM usage.

## Generative AI Training Category

The act of training General Purpose AI models that have the capacity to generate text, images or other forms of synthetic content, or the act of training other types of AI models that have the purpose of generating text, images or other forms of synthetic content.

The use of assets for Generative AI Training is a proper subset of AI Training usage.


# Usage

The vocabulary is used by referencing the terms defined in the {{vocab}} section above, directly or via mappings, in accordance with how they are defined in this document.

## More Specific Instructions

A recipient of a statement of preferences that follows this model might receive more specific instructions
in two ways:

* Extensions to the vocabulary might define more specific categories of usage.
  Preferences about more specific categories override those of any more general category.

* Statements of preferences are general purpose, machine-readable statements
  that cannot override contractual agreements or more specific statements.

For instance, a statement of preferences might indicate that the use of an asset is disallowed for AI Training.
If arrangements, such as contracts exist that explicitly permit the use of that asset, those arrangements likely apply, unless the terms of the arrangement explicitly say otherwise.

The vocabulary does not preclude the use of other specific categories. Any statement of preference based on this vocabulary shall not be interpreted as restricting the use of the work(s) strictly for the purpose of search and discovery as long as no restriction is declared through search-specific means such as {{!RFC9309}}.

## Vocabulary Extensions

Systems referencing the vocabulary must not introduce additional categories that include existing categories defined in the vocabulary or otherwise include additional hierarchical relationships.


# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.

--- back

# Acknowledgments
{:numbered="false"}

The following individuals have been involved in the drafting of the proposal:

* Cullen Miller, Spawing.ai
* Sebastian Posth, Liccium
* Leonard Rosenthol, Adobe
* Laurent Le Meur, EDRLab
