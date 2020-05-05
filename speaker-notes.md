# Speaker Notes

Turns out state is really really hard to get right.

Everytime you need to restart your computer or an app is probably because state went bad.

You fix it by blowing away the old state and starting fresh.

In fact state is so hard we had to build debuggers: that's right, people had to build programs to help other programmers figure out the state in their programs.

> "I understand WHY it crashed. I just don't understand how the user could have possibly gotten it in that state." ← Programmer adds another state check.

---

For communication between two components that don't have a parent-child relationship, you can set up your own global event system. Subscribe to events in `componentDidMount`, unsubscribe in `componentWillUnmount`, and call `setState` when you receive an event. The Flux pattern is one of the possible ways to arrange this.

- Components are given callback functions as props, which they call whenever a UI event happens.
- Those callbacks create and dispatch actions based on the event.
- Reducers process the actions, computing the new state.
- The new state of the whole application goes into a single store.
- Components receive the new state as props and re-render themselves where needed.

---

An action is a plain JavaScript object that describes in a minimal way what needs to get updated in the application state.

Actions are a way to trigger state mutations: they describe what should change, albeit having no idea how to make that change.

---

Reducers are pure functions which describe state mutations.

Unlike actions, reducers do know how to have things changed in the application state

---

The store is the glue that holds all of the Redux building blocks together:

- the state tree → single source of truth
- the action dispatcher
- the application reducers

---

Exposed Store Methods

Declarative UI libraries can leverage `subscribe` to trigger a re-render of our view layer.

Subscribe returns an `unsubscribe` function.

---

Reducer composition means that a reducer can call or be called by another reducer. This is useful as it allows us to move from a single, gigantic reducer handling all possible (unrelated) actions to a set of specialised, independent reducers focusing on a single part of our application state.

As a result, our top level reducer will be made out of a bunch of other smaller reducers.

---

The first time this runs, state.counter and state.alert will both be undefined. This means reducers will end up returning their corresponding initial value, thus populating the store for the first time.

When an action comes in, all of our specialised reducers get called every single time… ALL OF THEM! Only those reducers knowing how to handle this action will return an updated model, while the rest will return their current state.

---

`combineReducers` lets us set the mapping b/w the state tree field names, and the reducers managing them.

The returned value is supposed to be a reducer itself, therefore its signature must match the reducer signature: f(state, action).

---

If you have a look at the second parameter of the reduce method, nestState starts off being an empty object and we gradually attach properties to it when running nextState[key] = ....

Finally, we are mutating the nextState object on each iteration. This is not a problem though, because we are mutating an object we have created ourselves inside the reducer, it isn't something we got passed in from the outside, meaning it's still a pure function.

---

Singleton: Possibly the simplest way to have a component access the store to read the state tree, trigger actions and subscribe to state changes is to directly reference it, something along the lines of...

This is not maintainable for two main reasons:

- Testing: this approach prevents us from providing mock store instances.
- Server-side Rendering (SSR): when building universal (isomorphic) applications we need to provide a different store instance for every request because different requests involve different data.

---

The Provider's job is to pass down the store to all of the connected components no matter how deep they are down the component tree.

This way we don't need to pass the store as a prop to our children: Provider does this for us automagically… well, sort of. It actually makes use of React's context feature.

This all means, instead of be passing down the store explicitly via props, we'll be passing it in implicitly via context.

---

`connect` is a curried function: its first application returns a HOC (higher-order component).

When we pass in our own component to this HOC, we are gonna have it turned into a container component connected to the Redux store.

---

`forceUpdate` makes the component re-render itself whenever the state tree changes. 

`forceUpdate` is a (discouraged) way to manually trigger the render method on a component.

Here we are subscribing to the store changes on `componentDidMount` and unregistering our component from the list of listeners whenever the component gets unmounted.

The HOC renders our component with its own props plus all of the additional props calculated from the Redux store: data and callbacks.
