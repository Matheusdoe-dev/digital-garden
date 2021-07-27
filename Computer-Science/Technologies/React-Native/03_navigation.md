# Navigation

## React Navigation

- React Navigation's `stack navigator` provides a way for your app to transition between screens and manage navigation history.

- If your app uses only one stack navigator then it is conceptually similar to how a web browser handles navigation state - your app _pushes and pops_ items from the `navigation stack` as users interact with it.

- A key difference between how this works in a web browser and in React Navigation is that React Navigation's stack navigator provides the gestures and animations that you would expect on Android and iOS when navigating between routes in the stack.

### Stack Navigator

- `createStackNavigator` is a function that returns an object containing 2 properties: `Screen` and `Navigator`. Both of them are React components used for configuring the navigator. The `Navigator` should contain `Screen` elements as its children to define the configuration for routes.

- `NavigationContainer` is a component which manages our navigation tree and contains the [navigation state](https://reactnavigation.org/docs/navigation-state/). This component must wrap all navigators structure. Usually, we'd render this component at the root of our app, which is usually the component exported from `App.js`

```tsx
// In App.js in a new project

import * as React from "react";
import { View, Text } from "react-native";
import { NavigationContainer } from "@react-navigation/native";
import { createStackNavigator } from "@react-navigation/stack";
```

## References

- [React Navigation docs](https://reactnavigation.org/docs/hello-react-navigation)
