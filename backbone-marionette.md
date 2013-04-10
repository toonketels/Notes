# Backbone Marionette

### Event aggregation

* Do _event aggregeation_. Everything in application should have access 
  to an object that can emit events. It should trigger on and listen
  events of this object.
  This allows for an pub-sub architecture
  @see
  * MyApp.vent()
  * Backbone.Wreqr.EventAggregator

* All other code is responsible for triggering events on this object.
  It does not aggregate automatically.

* Extend any object with Backbone.Events to be able to trigger, 
  listen to events.


### Application object

* _Application object_ does
  - emit events when application start
  - initializes application
  - allows custom initializer functions to be added when starting or 
    dynamically after start, options object to start passed to these
  - allows for regions to be added
  - accepts an options object to be passed to each initializer function


### Modules

* _Modules_ can be started/stopped dynamically which means they
  can be loaded asynchronously and easier tested (only start this
  one module).

* Mimik api main application as module => .start() is also
  used to start modules.

* Trigger specific events on module when starting like before start
  and after start.

* Flow:
  - register them / add them
  - start them
  - stop them

* Stopping a module means that it no longer consumes resources. Fe
  a logger would stop sending logmessages over tcp of all events.

* In _finalizers_ you are responsible to tear down and clean up
  your module.


### Approuter

* Difference with bare backbone is that a router by default calls
  the callback on a controller, which is just an object with route
  callbacks. The reason is because you often when a route to call
  a method on another object, so cut boilerplate and pass the object
  directly.

* Controller can be any object, does not need to be a `controller`
  object.

* Recommended to use multiple routers/controllers instead of one
  router/controller.


### Controller

* A multipurpuse object with some stuff build in so it's easy to use
  in application:
  - initialize
  - extends backbone.Events
  - close method

* No mvc controller.


### Commands

* Application level command execution system to register commands
  and other modules/code can call execute it at will (command pattern). 

* Fe. define a log command. Anybody can execute it.

* There can be only one handler (the code that actually gets executed).
  Register a new handler with an existing name, will override it.

* Difference with event aggregation:
  - event is, something happened, you need to listen on it for a 
    specific emitter (not in case of aggregation).
  - command is an action. What happens is already defined. Anybody
    can trigger.
  - events can be handled multiple times, with different actions.
    Emitting an event does not state intent how it should be handled,
    commands do.

* Actions to do:
  - register
  - execute
  - remove/replace

* Provided by @see wreqr


### Request Response

* Application level request/response system.

* Same as commands but we new expect a response.

* @see wreqr



### Callbacks

* Keep a list of functions to be executed, execute them all at once,
  functions added later then the run event will still be executed 
  (guaranteed).

* Methods: add, run, reset


### Helper Functions

* _triggerMethod_ not only triggers an event but will also
  call a method like onEvenNameOptions on the target object.

* _bindEntityEvents_ allows for shortcut eventHandling in fe views.
  Just like we already had events: {}, we now can have modelEvents: {}.
  Entity refers to model/collections.


### Renderer

* Object extracted from itemView to modify only one item to 
  change templating engine...

* We can use precompiled templates, may need to override render method.

* Template functions are fetched form templateCache to not convert template
  to function again and again.


### Babysitter

* Keeps indexes on subviews for a given collectionView to easily get 
  a subview based on its model, its collection...


### TemplateCache

* Cache compiled templates (which are functions) so a template
  needs to be compiled only once.

* We need to override some methods when making use of different template engines.


### Regions

* Custom object defined to render views inside.

* Allows modifications by overriding methods. Override show() 
  & close() for custom animations.

* Can be attached when already rendered serverside (existing).

* When dynamically adding regions (not during init?) we need to
  manually call initialization procedure.


### Layout

* Dynamic layouts. It's a view that's inserted into a region which
  can also display regions.

* Has functionality to cleanup all subregions and views => kill zombies.

* Inherits from item-View.

* Since rerendering will cause all subviews and regions to be closed
  and reatttached again, it's adviced to listen for model's
  change events and update DOM specifically.


### Marionette View

* Base view all other view types extend. Dont use directly.

* Always use "listenTo" in views (instead of "on") because it gets unbound automatically
  on "close". ListonTo makes the view keep track of all the callbacks.

* Povides Close() method engineered to prevent zombie views by unbinding all listenTo
  events, all view events, all dom events, removing this.el from DOM and firing custom
  closing events. Hook into these custom closing events to do additional cleanup (so
  dont override close).

* Prevent view from being closed by return false in onBeforeClose().

* Hook into onDomRefresh (event) to display widget since it's a sign the view
  has completely rendered and ready into the DOM.

* Shortcut "triggers" hash to invoke custom view events whenever a
  specific dom event occures (make it easy so we never watch for dom events again).
  Argument passed is {view: '', model: '', collection: ''}. 
  Difference with "events" is that the latter specifies views methods to invoke while
  the formar specifiers which events the view will trigger.
  Makes it easer to emit events.

* Shortcut `modelEvents` and `collectionEvents` hashes to invoke view callbacks
  on specific model/collection events. Just like `events` but instead of listening to
  DOM events, it listens to model/collection events. 
  Invoke multiple callbacks for such an event by defining them in the hash seperated
  by a space.
  Function can also be declared inline.

* View's `serializeData` method to serialize collection or model data.

* Shortcut `ui` hash of {name: selector} to stop querying dom with jquery but
  use the var instead. Change the view or dom element, only change it once in
  the ui hash.

* Invoke functions from withing logicless templates with templateHelpers. Use
  `this` in these functions to access other templateHelpers and serialized data.

* Alternate template used based on model data with `getTemplate` method.


### ItemView

* Can be used for models or collections. It's called itemView because
  it's treated as a single item (collection as a thing, without rendering
  the models in it).

* Different events and their corresponding onEventName methods are 
  emitted/called:
  - onBeforeRender
  - onRender
  - onDomRefresh
  - onBeforeClose
  - onClose

* Rendering is done by Renderer object. Uses `template` attribute/method.
  Make it a function to dynamically vary templates used or custom data fed
  based on serialized model/collection data.

* @see view for ui, triggers, modelEvents, collectionEvents hashes...


### CollectionView

* View to render items in a collection (so has subviews).

* We can define a "noResultsFound" view via `emptyView`.

* Uses babysitter to easily get subviews based on model...
  Access it by collectionView.children.

* ItemView events are forwarded as itemview:eventname. This 
  `itemView` prefix can be customized.

* Use `itemViewOptions` to pass a hash or function return hash
  as "fixed" options to each itemView constructor.

* Next to itemViews events/callbacks, calls callbacks and emits
  events when itemViews are added, removed.

* Override appendHTML to animate how itemViews are appended.


### CompositeView

* A view for both rendereing a model and a collection at the same time?

## How are things decoupled in Marionette

* Application level command system (communication)
* Application level request/response system (communication)
* Application level event aggregration: one object to listen for events (communication)
* Module system to dynamically add code (booting, adding functionality)
* By keeping indexes on collection to easily get a subview from collectionview
  based on model (babysiter). No need to keep a pointer from model to itemView. (model - views)


## How makes Marionette it easier to be an evented framework?

* View `triggers` hash to automatically emit custom events on views when
  dom events occur. Arguments are standardized.
* Event argregator object.
* Automatically prefix collectionView's itemviews events.


## How to animate views?

* Use regionManager to animate how an entire view is displayed in the dom.
* Use onRender method/events within view to animate a view just after
  is has been inserted into the dom.
  => only insert titles, onRender animate the body and image visible.
* Use onBeforeClose to animate stuff before view is removed from dom.
* Override appendHTML is collectionViews to animate how itemViews
  are added (?).
* Use different layout wich will load different regionManagers which
  have different animations.


## How can we customize layouts?

* Override collectionView's `getItemView` to use a different _view_
  depending on the model or something else.
* Override itemView's `template` to use a different template depending
  on model's attributes.
* Use `layout` object to create new regions based on collections or model.


## Naming

* _methodName for private methods.
* initalizors and finalizers.


## Ideas

* Allow a modular system to dymamically load code. This means we need
  a system to add the code and intialize it as the rest of app is
  already initialized.
  Start few functionality, start quick
    => add modules
      => more functionality

* Try to use multiple routers in an app.

* A module that specifies a log command.


## Question

* Why extending an object itself and also it's prototype object?
* Why is addRegions and buildRegions in applciation object not called
  automatically during intializaton/boot?
  => to allow flexibility?
* Difference between itemView and collectionView?test
