---
title: "Linked Art API: Assigned Relationship Structure"
---

<style>
th, td {
  padding: 5px 5px;
  text-align: left;
  border: 1px solid #D0D0D0; }
th { background: #F0F0F0; }
th:first-child, td:first-child { padding-left: 3px; }
th:last-child, td:last-child { padding-right: 3px; }
</style>

[TOC]

## Introduction

It is often useful to have direct relationships between entities, such as objects that have some (unstated) relationship to each other, inter-personal relationships that are too convoluted to express semantically, or just an arbitrary set of recommendations for other entities that might be of interest to a consuming application and its users. In order to manage such arbitrary, non-semantic relationships, the AttributeAssignment activity is used with the optional `assigned_property` to give an identifier for the relationship, if known. 

## Property Definitions

The relationship assignment data structure has the following properties.

### Properties of Assignments

| Property Name     | Datatype      | Requirement | Description | 
|-------------------|---------------|-------------|-------------|
| `id`              | string        | Optional    | If present, the value MUST be a URI identifying the assignment |  
| `type`            | string        | Required    | The class for the assignment, which MUST be the value `"AttributeAssignment"` |
| `_label`          | string        | Recommended | A human readable label for the assignment, intended for developers |
| `identified_by`   | array         | Recommended | An array of json objects, each of which is a name of the assignment and MUST follow the requirements for [Name](../../shared/name/), or an identifier for the assignment and MUST follow the requirements for [Identifier](../../shared/identifier/)|
| `classified_as`   | array         | Recommended | An array of json objects, each of which is a further classification of the assignment and MUST follow the requirements for [Type](../type/) |
| `referred_to_by`  | array         | Optional    | An array of json objects, each of which is an embedded [statement](../statement/) about the assignment |
| `carried_out_by`  | array         | Optional    | An array of json objects, each of which is a [reference](../reference/) to a [Person](../../endpoint/person) or [Group](../../endpoint/group) which made the assignment|
| `timespan`        | json object   | Optional    | A json object which describes when the relationship was assigned, and MUST follow the requirements for [timespans](../timespan/)|
| `assigned`        | array         | Required    | An array of json objects, each of which is a [reference](../reference/) to another entity that is related to the current one |
| `assigned_property` | string      | Optional    | A string which is either a URI, or resolves to a URI via a context document, for the specific relationship between the main entity and the entity referenced in `assigned` |


### Property Diagram

> ![diagram](assignment_properties.png){:.diagram_img width="600px"}

### Incoming Properties

Relationship Assignment instances are typically found as the object of the following properties.  This list is not exhaustive, but is intended to cover the likely cases.

| Property Name   | Source Endpoint   | Description |
|-----------------|-------------------|-------------|
| `attributed_by` | All Endpoints     | A list of Relationship Assignments for the entity |


## Example

A Human-Made Object [instance](../../endpoint/physical_thing/):


```crom
top = model.HumanMadeObject(ident="auto int-per-segment", label="Painting of a Fish")
aa = model.AttributeAssignment()
aa.carried_out_by = model.Person(ident="auto int-per-segment", label="Curator")
aa.identified_by = vocab.DisplayName(content="Related Object: Another Painting of a Fish")
aa.classified_as = model.Type(ident="http://example.org/types/recommending", label="Recommendation")
aa.assigned = model.HumanMadeObject(ident="auto int-per-segment", label="Another Painting of a Fish")
top.attributed_by = aa
```
