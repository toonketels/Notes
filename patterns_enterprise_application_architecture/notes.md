# PATTERNS OF ENTERPRISE APPLICATION ARCHITECTURE

## 1. LAYERING

To deal with complexity we create distinct layers in an application.

A layer is a boundary of functionality at a certain level. Layers
are stacked on top of each other. Layers are not aware of other layers
except for their touching (upper and) lower layer.

A layer should provide a consistent API to the layer above.

New layers can be build on top of old layers. The programmer should
only know the public API's of that layer, not the internals.

A layer can be thought of as a level of abstraction.

Example of layered structure is the different networking layers
of the Internet.

### Three principal layers in enterprise applications.

* Presentation
* Domain
* Data source

_Presentation_ is about capturing user intent (to perform actions) and
displaying data back to the user.

_Domain_ is about business logic, validation... The real deal of the app.

_Data source_ is about communication with other systems such as database
and make data persistent.

It is best to always have this separation. For the simplest system with
one file, separate the "layers" through functions.

The clearer and consistent this separation, the easier to reason,
maintain and change.

### Tip in separating functionality in layers

1. Know that most should live in domain.
2. To separate between presentation and domain ask: "If I had to
   implement an alternative UI (think CLI/browser), which code would
   have been duplicated?". Put this code into the domain.
3. To separate between domain and data source ask: "If I had to swap
   data source (xml/mysql/mongo/elastic search), which code should I
   have to write?". This code is probably functionality which exists
   in your current data source which belongs in the domain.

### Notes

Data source applications like mysql will provide useful functionality
like validation and transactions. These "should" live in the domain.
Relying on them will speed up development but makes it hard to swap data
source. In reality, we hardly ever swap data source (but be love the thought).

In the beginning, some domain logic may live in the presentation and
we should not be dogmatic about it. Just know to refactor when
complexity rises.





