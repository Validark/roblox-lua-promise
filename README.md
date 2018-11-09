# Roblox Lua Promise
A fork of LPGhatguy's [roblox-lua-promise](https://github.com/LPGhatguy/roblox-lua-promise), modified for use in roblox-ts.

## API

### Static Functions
* `Promise.new((resolve, reject, onCancel) -> nil) -> Promise`
	* Construct a new Promise that will be resolved or rejected with the given callbacks.
	* You may register an optional cancellation hook by using the `onCancel` argument.
		* This should be used to abort any ongoing operations leading up to the promise being settled. 
		* Call the `onCancel` function with a function callback as its only argument to set a hook which will in turn be called when/if the promise is cancelled.
		* When a promise is cancelled, calls to `resolve` or `reject` will be ignored, regardless of if you set a cancellation hook or not.
* `Promise.resolve(value) -> Promise`
	* Creates an immediately resolved Promise with the given value.
* `Promise.reject(value) -> Promise`
	* Creates an immediately rejected Promise with the given value.
* `Promise.is(object) -> bool`
	* Returns whether the given object is a Promise.
* `Promise.all(array) -> array`
	* Accepts an array of promises and returns a new promise that:
		* is resolved after all input promises resolve.
		* is rejected if ANY input promises reject.
	* Note: Only the first return value from each promise will be present in the resulting array.

### Instance Methods
* `Promise:andThen(successHandler, [failureHandler]) -> Promise`
	* Chains onto an existing Promise and returns a new Promise.
	* Equivalent to the Promise/A+ `then` method.
* `Promise:catch(failureHandler) -> Promise`
	* Shorthand for `Promise:andThen(nil, failureHandler)`.
* `Promise:finally(finallyHandler) -> Promise`
	* Set a handler that will be called regardless of the promise's fate. The handler is called when the promise is resolved, rejected, *or* cancelled.
	* Returns a new promise chained from this promise.
* `Promise:cancel() -> nil`
	* Cancels this promise, preventing the promise from resolving or rejecting. Does not do anything if the promise is already settled.
	* Cancellations will propagate upwards through chained promises.
		* Promises will only be cancelled if all of their consumers are also cancelled. This is to say that if you call `andThen` twice on the same promise, and you cancel only one of the promises resulting from the `andThen` call, it will not cancel the parent promise until the other child promise is also cancelled.
* `Promise:await() -> ok, value`
	* Yields the current thread until the given Promise completes. Returns `ok` as a bool, followed by the value that the promise returned.
* `Promise:getStatus() -> Promise.Status`
* `Promise:isRejected() -> bool`
* `Promise:isResolved() -> bool`
* `Promise:isCancelled() -> bool`
* `Promise:isPending() -> bool`

## Example
This Promise implementation finished synchronously. In order to wrap an existing async API, you should use `spawn` or `delay` in order to prevent your calling thread from accidentally yielding.