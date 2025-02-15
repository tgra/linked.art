---
title: "Collections and Sets"
---

[TOC]

## Introduction

There are many use cases for grouping resources together, often of the same class but sometimes of varying types.  These use cases are exemplified in the sections below, and range from the set of objects in an auction lot, to dealer inventories and museum collections, exhibitions, a set of related concepts, or the set of people that share a common feature such as gender or nationality.

In order to cover all of the use cases with a consistent pattern, we introduce a new `Set` class from outside of CIDOC-CRM. This avoids issues with sets of resources with different types, and the semantics of the identity of objects and collections. If an equivalent class is added into the core CIDOC-CRM ontology in the future, a new major version of the specification will change to using it.

## Sets

### Core Features

Sets are conceptual groupings, rather than physical ones.  The set of objects in a virtual exhibition or simply the set of a person's favorite objects never change their physical state by being part of the Set or not.  They are, thus, created by a `Creation`, not by a `Production`.

Like any core resource, Set must have an `id` and `type`, are likely to have additional classifications, and can have Identifiers and Names. They can have statements made about them, and have member resources.  These member resources are included via the `member` property rather than `part`, or via `member_of` from the included resource to the Set.  

```crom
top = vocab.CollectionSet(ident="auto int-per-segment")
top._label = "Example Collection"
p = vocab.Painting()
p._label = "Example Painting"
top.member = p
desc = vocab.Description()
desc.content = "This is a lovely little collection"
top.referred_to_by = desc
n = model.Name()
n.content = "Example Collection"
top.identified_by = n
cre = model.Creation()
ts = model.TimeSpan()
ts.begin_of_the_begin = "1954-01-01"
ts.end_of_the_end = "1955-01-01"
cre.timespan = ts
top.created_by = cre
```


### Collections of Objects

Sets can be used to describe the set of objects that make up a curated collection. This is not necessarily the set of objects that the institution owns, as there could be objects which are looked after but owned by some other organization or individual, nor the set of objects that the institution has custody over, as objects being loaned to other organizations for exhibitions are still part of the conceptual collection of objects. The details of the relationship between the object and the institution are recorded on the object, and the Set simply records which objects are thought of as being part of the collection.  Objects can be thought of as being part of multiple collections at the same time -- the private owner's personal collection and the museum's public collection.  So while the majority of objects are both owned by and in the custody of the organization, this is not certain and should not be inferred. The Set is created by the organization or person that abstractly manages the collected objects.

This pattern can be used for any type of organization that manages objects, from museums and archives to individuals and art dealers.

```crom
top = vocab.CollectionSet(ident="auto int-per-segment")
top._label = "Collection of Example Museum"
p = vocab.Painting()
p._label = "Example Painting"
top.member = p
cre = model.Creation()
inst = vocab.MuseumOrg()
inst._label = "Example Museum"
cre.carried_out_by = inst
top.created_by = cre
```

#### Departmental Collections

Institutions are often split up into departments, each of which will manage a part of the overall collection. These parts of the collection are managed as separate Sets, rather than a tree structure within a single resource.  It is useful to be able to describe the properties of the object in each of the contexts, and allow a separate structure of inventory management from organizational chart. The department might also conceive of further sets of their objects, without any direct correspondence and likely with the same object being part of more than one set at the same time.

```crom
top = vocab.CollectionSet(ident="auto int-per-segment")
top._label = "Collection of Example Museum's Paintings Department"
p = vocab.Painting()
p._label = "Example Painting"
top.member = p
cre = model.Creation()
inst = vocab.MuseumOrg()
inst._label = "Example Museum"
dept = vocab.Department()
dept._label = "Paintings Department"
dept.member_of = inst
cre.carried_out_by = dept
top.created_by = cre
```

### Other Uses

The set of objects in an [auction lot](/model/provenance/auctions.html#set-of-objects) are also modeled as a Set. These are not curated in the same way as a museum collection, and are not necessarily ever brought together physically, but are being put up for auction as a single entity.  Similarly, the set of objects used in an [exhibition](/model/exhibition/#objects) is also modeled as a Set.

### Collection Specific Information

Information about a resource that is specific to the context of the set that they are part of, such as the accession number of an object for that particular collection, can be described using the `AttributeAssignment` patterns described in the page about [assertions](/model/assertion/#context-specific-assertions).


## Collections over Time

Collections are not static over time but instead change as objects are acquired and sold, stolen or given to new owners. Recording these states results in some complexity, as we still want to be able to refer to objects in a previous context, rather than just their current context. It is useful, for example, to record the accession or stock number of an object in previous collections or as assigned by dealerships and auction houses.  For collections that never change once finished, this isn't a problem.  The object can be considered to always be part of the auction lot.  However for museum or dealer collections, it is important to distinguish between the objects that are currently part of that collection and the objects that have ever been part of the collection. 

Much of the time this is an unnecessary complication that can be safely ignored, as the information systems only know about the current state. If it is important, then read on.

### Adding and Removing Resources

Objects are rarely added and removed from collections without further contextual information, there is almost always some larger activity that results in the change.  The ownership of the object is transferred to the organization which prompts it to be accessioned into the collection, and subsequently an accession number Identifier as assigned to it.

There are two activities, `Addition` and `Removal`, which mirror the same activities of Joining and Leaving for members of Groups.  These may be included in the object representation, to further expand upon the `member_of` relationship.

```crom
top = vocab.Painting(ident="auto int-per-segment", label="Painting", art=1)
coll = vocab.CollectionSet()
top.member_of = coll
add = model.Addition()
top.added_member_by = add
add.added_to = coll
add.carried_out_by = model.Person(label="Museum Registrar")
ts = model.TimeSpan()
ts.begin_of_the_begin = "1954-01-01T00:00:00Z"
ts.end_of_the_end = "1955-01-01T00:00:00Z"
add.timespan = ts
```
