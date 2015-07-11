Event Dispatcher Interfaces
========================

This document describes a common interface for creating events and event dispatchers.

The main goal is to allow libraries to receive a Psr\EventDispatcher\EventInterface object and allows users to optionally inject functionality into the library or application. Frameworks and CMSs that have custom needs MAY extend the interface for their own purpose, but SHOULD remain compatible with this document.

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).

## 1. Specification
### 1.1 Terms

*   **Event** - An action that is about to take place (or has taken place).  The event name MUST only contain the characters `A-Z`, `a-z`, `0-9`, `_`, and '.'. It is RECOMMENDED that words in event names be separated using '.' (example 'foo.bar.baz.bat').
*   **Listener** - A list of callbacks that are passed to the EventInterface and MAY return a result. Listeners MAY be attached to the ```EventDispatcher``` with a priority.  Listeners MUST be called based on priority.

### 1.2 EventInterface

* The ```EventInterface``` defines the methods needed to dispatch an event.  Each event MUST contain an event name in order to trigger the listeners. Each event MAY have a target which is an object that is the context the event is being triggered for. OPTIONALLY the event can have additional parameters for use within the event.
* The Event MUST contain a propagation flag that signals the ```EventDispatcher``` to stop passing the event along to other listeners.
* The Event can OPTIONALLY be immutable. For this reason there are no set methods in the interface.

### 1.3 EventDispatcherInterface

* The EventDispatcherInterface holds all the listeners for a particular event.  Since an event can have many listeners that each return a result, the ```EventDispatcherInterface``` MUST return the ```EventInterface``` from the last listener.

### 1.4 Helper classes and interfaces

* The ```Psr\EventDispatcher\DispatcherAwareInterface``` only contains a ```setDispatcher(EventDispatcherInterface $dispatcher)``` method and can be used by frameworks to auto-wire arbitrary instances with a dispatcher.
* The ```Psr\Log\DispatcherAwareTrait``` trait can be used to implement the equivalent interface easily in any class. It gives you access to ```$this->dispatcher```.

## 2. EventDispatcherInterface

```php

namespace Psr\EventDispatcher;

/**
 * Interface for EventDispatcherInterface
 */
interface EventDispatcherInterface
{
    /**
     * Attaches a listener to an event
     *
     * @param string   $eventName The event to remove a listener from
     * @param callable $callback a callable function
     * @param int      $priority the priority at which the $callback executed
     *
     * @return bool true on success false on failure
     */
    public function addListener($eventName, callable $callback, $priority = 0);

    /**
     * Removes an event listener from the specified events.
     *
     * @param string   $eventName The event to remove a listener from
     * @param callable $listener  The listener to remove
     *
     * @return void
     */
    public function removeListener($eventName, callable $listener);

    /**
     * Dispatches an event to all registered listeners.
     *
     * @param EventInterface  $event  The event to pass to the event handlers/listeners.
     *
     * @return EventInterface
     *
     * @api
     */
    public function dispatch(EventInterface $event = null);
}
```

## 3. EventInterface

```php

namespace Psr\EventDispatcher;

/**
 * Representation of an event
 */
interface EventInterface
{
    /**
     * Get event name
     *
     * @return string
     */
    public function getName();

    /**
     * Indicate whether or not to stop propagating this event
     *
     * @param  bool $flag
     */
    public function stopPropagation();

    /**
     * Has this event indicated event propagation should stop?
     *
     * @return bool
     */
    public function isPropagationStopped();

    /**
     * Get argument by key.
     *
     * @param string $key An identifying key.
     *
     * @throws \InvalidArgumentException If key is not found.
     *
     * @return mixed Contents of array key.
     */
    public function getArgument($key);
}
```
