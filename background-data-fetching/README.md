# ![Fetching Data in React - Background Data Fetching](./assets/hero.png)

**Learning objective:** By the end of this lesson, students will be able to trigger data fetches with the `useEffect()` hook.

## Background data fetching

So far, we've learned how to fetch data based on user interactions, such as clicking a button or submitting a form. However, there are instances where we might want data to be retrieved automatically. For example, when a page first loads (or component first mounts), we might want to display a list of products. Additional user actions might refine these results, but we still want some initial data to load when the site is accessed.

To achieve this background data fetching, React provides a hook called `useEffect()`. 

> 💡 You might notice that the React docs encourage developers to handle data fetching with a React framework or a data fetching library. While these are valid options, we'll focus on implementing background data fetching with the built in tools provided by React. In doing so, we'll gain an understanding of a valuable hook.

## Understanding the `useEffect()` hook

The `useEffect()` hook is used to handle **side effects within functional components**. A side effect is any action that interacts with something outside the functional component, including **data fetching**. 

Essentially, the hook lets us define what code needs run after a component renders. For our purposes, this will include fetching data.

Let's take a look at the anatomy of a `useEffect()`:

```jsx
useEffect(() => {
  // Place side effects like data fetching here!
}, [dependency]);
```

The `useEffect()` method accepts two arguments:

1. An anonymous callback function (`() => {...}`) where you can execute side effects like data fetching. Inside this callback, it's common practice to **declare and invoke** a separate function dedicated to fetching data. This fetching function often utilizes a `setState()` function to update the component's state with the retrieved data.

2. An optional dependency array (`[]`). This array should contain variables or pieces of state that, upon changing, will cause the `useEffect()` hook to call upon the provided callback function again. Any value referenced inside the `useEffect()` callback that might change over time, should be included as a dependency. This should ensure that the effect runs at the correct times.

By default, `useEffect()` executes its callback after every render of the component, but this behavior can be controlled with the dependency array. 

- If the dependency array is left out, the side effect function runs after every render.

- If the dependency array is empty (`[]`), the side effect function runs once after the initial render.

- If dependencies are included, the side effect function runs whenever the dependency data changes.

Though the dependency array is optional, you will typically want to include it. An empty dependency array will limit function calls to the initial mounting of the component, which cuts down on unexpected behavior as you build out the hook. 

> 🚨 A common pitfall with the `useEffect()` hook is unintentionally triggering infinite loops. Such loops can occur if your `useEffect()` makes updates state without managing the dependency array correctly. Omitting necessary dependencies or incorrectly handling them can lead to an effect that triggers a rerender, which in turn reinvokes the same effect in a never ending cycle!

## Fetching default weather data

Let's see how `useEffect()` can be practically implemented in our weather app. 

First, update the existing import in `src/App.jsx` with this new hook -  `useEffect`:

```jsx
// src/App.jsx
import { useState, useEffect } from 'react';
```

Above the `return` in `src/App.jsx`, build out the scaffolding for `useEffect()`:

```jsx
// src/App.jsx
  useEffect(() => {

  }, []); // An empty dependency array means this runs once after the initial render
```

Inside our `useEffect()`, we'll define a new function called `fetchDefaultData`. It will closely resemble our existing `fetchData` function, but we'll go through the process of making a new function to avoid issues with our dependency array. Let's have our default fetch function retrieve weather information on `'New York`', and set that to `weather` state.

Update your `useEffect` with the following:

```jsx
// src/App.jsx
  useEffect(() => {

    // Define a fetch function:
    const fetchDefaultData = async () => {
      const data = await weatherService.show('New York');
      const newWeatherState = {
        location: data.location.name,
        temperature: data.current.temp_f,
        condition: data.current.condition.text,
      };
      setWeather(newWeatherState);
    };

  }, []);
```

If you check your browser, you may notice that our code so far has not had an effect on `weather` state. This is because we have only **defined** the `fetchDefaultData` function within our `useEffect()`. At the moment, everytime the `useEffect()` hook runs, it recreates the `fetchDefaultData` function, and nothing else. The next step is to **call** the `fetchDefaultData` function within the `useEffect()` callback.

Update your `useEffect` with the following:

```jsx
// src/App.jsx
  useEffect(() => {

    const fetchDefaultData = async () => {
      const data = await weatherService.show('New York');
      const newWeatherState = {
        location: data.location.name,
        temperature: data.current.temp_f,
        condition: data.current.condition.text,
      };
      setWeather(newWeatherState);
    };

    // Call the fetch function when the page loads:
    fetchDefaultData();

  }, []);
```

Try it out in your browser! When the page loads, you should see weather information for New York appear as a default.