## Typing **sweet-state** with Flow

This is a basic example:

```js
import { createStore, createSubscriber, createHook, createContainer, type Action } from 'react-sweet-state';

type State = {| count: number |};

const initialState: State = {
  count: 0,
};

const actions = {
  increment: (by = 1): Action<State> => ({ setState, getState }) => {
    setState({
      count: getState().count + by,
    });
  },
};

type Actions = typeof actions;

const Store = createStore<State, Actions>({
  initialState,
  actions,
});

const CounterSubscriber = createSubscriber<State, Actions>(Store);
const useCounter = createHook<State, Actions>(Store);
const CounterContainer = createContainer<State, Actions>(Store);
```

#### Actions pattern

If your actions require `Container` props:

```js
type ContainerProps = {| multiplier: number |};

const actions = {
  increment: (by = 1): Action<State, ContainerProps> => (
    { setState, getState },
    { multiplier }
  ) => {
    setState({ count: getState().count + by * multiplier });
  },
};
```

And if you need the `actions` key correctly typed:

```js
type Actions = typeof actions;

const actions = {
  fetch: (): Action<State, void, Actions> => ({ actions }) => {
    /* ... */
  },
};
```

#### createSubscriber / createHook patterns

If you provide a selector to your components, you need to defined two additional flow arguments to `createSubscriber`/`createHook`: the selector output and the selector props.

```js
type SelectorState = boolean;
const selector = (state: State): SelectorState => state.count > 0;

// this component does not accept props
const CounterSubscriber = createSubscriber<State, Actions, SelectorState, void>(Store);

// this hook does not accept arguments
const CounterSubscriber = createSubscriber<State, Actions, SelectorState, void>(Store);
```

In case your component/hook needs also some props, you can define them as fourth argument:

```js
type SelectorProps = {| min: number |};
type SelectorState = boolean;
const selector = (state: State, props: SelectorProps): SelectorState => state.count > props.min;

// this component requires props
const CounterSubscriber = createSubscriber<State, Actions, SelectorState, SelectorProps>(Store);

// this hook requires an argument
const useCounter = createHook<State, Actions, SelectorState, SelectorProps>(Store);
```

#### createContainer patterns

If your container requires additional props:

```js
type ContainerProps = {| multiplier: number |};

// this component requires props
const CounterContainer = createContainer<State, Actions, ContainerProps>(Store);
```
