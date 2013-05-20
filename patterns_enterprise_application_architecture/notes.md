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


## 2. ORGANIZING DOMAIN LOGIC

Since the domain is the meat of the application, we should take care
organizing it.

3 primary patterns to organize domain
* transaction script
* domain logic
* table module

### Transaction script

A "script" that runs whenever a certain action is taken and which
completes with a certain data being presented. All the actions, 
calculations... are contained within the script.

+ Easiest to start with
+ Easy to understand
- Can grow unyieldingly for complex systems
- Can result in a lot of code duplication for complex systems.

Note that a "script" does not need to be in one file, or can not
share code with other transaction scripts.

### Domain model

Object oriented model of the domain. After investigation the different
players in the problem space are identified. These become the objects
(nouns). Different actions become methods on the objects.

When a certain action needs to be taken, the thread passes through
all the different objects which have their say.

+ Easiest to reason about when complex systems
+ Most scalable (just add objects)
+ Prevent code duplication
- Hard for new developers to understand the specific set up
- Complex to set up 

### Table module

I don't really understand this one.

Bares some resemblance with domain model in the way that the different
players are objects. Unlike the domain model (where we have multiple
instances of these objects) we only have one instance per object and
a record to identify the specific data. Used in .net.

Makes use of record sets (which I don't know either).

+ Easier mapping on underlying data structure?

### Further division with service layer

The domain layers is sometimes divided further into a service layers and
the underlying domain layer.

The service layers is meant to provide a public/consitent API.

It's adviced to keep it as slim as possible.


## 3. MAPPING TO RELATIONAL DATABASES

The domain model (objects) need to be made persistent to a (relational)
database.

There are ways to do this but it may boil down to:
* using queries directly
* provide some kind of abstraction so objects are used to map the
  domain model onto relational structure

The problem is that the relational database and domain model don't naturally
fit. An [object-relational mapping](http://en.wikipedia.org/wiki/Object-relational_mapping) objects
can be used to translate the object into a relational structure. This
gives the benefit that the queries only need to be updatd on one place.

In general the data-source layer has one object per table or one per record.

### The behavioral problem

The behavior problem is the problem about how to load and save objects
back to the database.

Problems with loading:
* who is responsible for loading items (what object has a the load method)
  (=> unit of work)
* object which is read should not be modified (consistency)
* an a load, you also want to get all the corresponding objects (sale, we want
  customer too), how to prevent getting a massive object graph (=> lazy load)
* when loading the same record twice from different parts of the code, how
  do we ensure they both get updated when code updates one (they are best
  references to the same object) (=> identify map)

Problems with saving:
* when should objects be saved (they just live in memory)

### Reading data

Reading data is about `find(id)` or `findForCustomer(customer) methods
to actually retrieve data from the db.

Where to add those methods:
* on one object per table: the object per table
* on one object per row:
  - or as a static (class method)
  - or as a new object => this is preferred

The risk with finders is that they only return the objects found from db,
not new ones only in memory.

For performance is best to minimize trips to db
* even if it means to retrieve more data then you need
* get more data back via joins (but try not more then 4)

### Connection

Creating a connection is expensive, managing it (keeping it open) too.
So we make some trade-off between keeping it open and opening, closing
connections.

Best to abstract the connection away so go get a hold on a connection,
code will call that object. The object will give an existing or create a
new connection. Our code does not need to worry about it.

### Object oriented databases

Instead of relational databases we can also use [object oriented databases](http://en.wikipedia.org/wiki/Object_database).

These tend to be more complex then pure relational databases but allow
objects to be serialized to the db. 


## 4. WEB PRESENTATION

Usually, scripts are best at processing response while pages are best at
displaying it.

To accommodate separation between processing and presentation use _MVC_ -
Model, View and Input Controller. It's the input controller to choose which
model to use, and once it receive the data back from the model, will
choose the appropriate view.

If the application is in control of the order of screen the user is
navigating we also need an _Application Controller_

### View patterns

(Transform View || Template View) * Two Step View

Transform view transform the (XML) output by applying different stylesheet?

Template view uses templates and placeholders and maybe a helper object
to pass the variables to the template. The risk exists too much logic 
resides within the templates.

Two Step is about compiling the template first, and having an different
application wide outer template. These days, it's all about nested templates.

### Input controller patterns

Page Controller || Front Controller

Front controller allows one object to control the entire application.


## 5. CONCURRENCY

Concurrency problems all have to do with multiple processes and threads
manipulating the same data.

Concurrency is hard because:
* hard to think off all the scenarios at play
* hard to test for

Enterprise applications deal with it mostly be _database transactions_
delegating the problem to the database. We still run into problems when
our data spans multiple transactions (offline concurrency) or when we 
deal with different threads application server.

### Concurrency problems

Some problems:
* lost updates - update while somebody else is updating 
* inconsistent read - grab multiple bags, grab one while the two bags are
  updated, grab second after update. The content is now partly before,
  partly after edit. This content is never correct (nor before, nor after).

Basic idea:
* for _correctness_, we best do everything sequential
* for _liveliness_, we best do everything concurrently
* choose your trade-off, or a system to manage concurrency (with its own
  set of problems)

### Execution contexts

Two contexts (in respect to outside world):
* request
* session: multiple consecutive requests tied to the same user (session)

Note, the server can be seen as:
* the server to the client => http sessions
* a client to db servers (or other servers it talks to) => db sessions

OS processes and treads:
* process: heavyweight execution context with lots of isolation internal
  data
* thread: lightweight, multiple within process, but do share memory

Ideally, processes or threads are associated with a session. Since this
very hard, they become associated with requests. A different architecture
is that of the event loop, which is just one process handling all the
requests.

In db land: a _transaction_ spans multiple requests and treats it as a
single request.

### Isolation and immutability

We mostly have two strategies in dealing with concurrency problems:
* isolation: ensure only one active agent can modify the data.
  OS does so with processes. Svn does this with locking a file before
  modifying.
* immutability: ensure data can't be changed. Not possible for all data
  but by making most data like this, we make it already easier for ourselves.

### Optimistic and pessimistic concurrency control

If data is not isolated and mutable:
* optimistic lock: allow modification but rejects latest save, up to the
  person to check what to do
* pessimistic lock: really locks a file. First one who locks it makes it
  impossible for others to acquire the lock.

To make matters worse, we can have problems with inconsistent reads or
deadlocks. 

These can be prevented with timeouts (loosing locks), deadlock detection
and choosing victim or a program to make you acquire all locks up front.

It's too easy to not consider all scenarios when creating your deadlock
proof schema so it's best to keep something conservative and simple.

### Transactions

Transactions must be ACID:
* Atomic: don't partially complete. It's an all or nothing game. If you
  can't complete, roll back. If you reach the end, commit.
* Consistent: the resources must be in a consistent non corrupted state
  at the start and end of the transaction
* Isolated: result transaction only visible to rest of system once committed.
* Durable: result must be made permanent (and should survive a crash). 
  So no, not in memory.

#### Transaction resources

The thing onto which we use transactions to control concurrency:
* db
* message queues
* printers

For maximum throughput (because of locks) keeps transactions as short
as possible.

Ways to balance correctness (trough isolation) vs liveliness:

* serializable: strongest, transactions are performend sequential after
  on another. No guarantees who comes first. But always in a correct state.

* repeatable read: whenever an action, the resources are read again to
  get the most accurate state.

* read committed: only read files once, don't poll to see if something
  has changed.

* uncommitted reads: get insight in uncommitted changes. Allows for dirty
  reads.

### Offline concurrency control

Optimistic Offline Lock || Pessimistic Offline Lock || 
Coarse-Grained lock || Implicit Lock

When dealing with concurrency do:
1. use transaction systems (like db transactions)
2. use long transactions that span multiple requests
3. use patterns here to deal with it in our application if we really 
   have to.


## 6. SESSION STATE



