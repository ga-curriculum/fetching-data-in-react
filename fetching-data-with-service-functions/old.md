
# ![Fetching Data in React - Making Fetch Requests in React](./assets/hero.png)

**Learning objective:** By the end of this lesson, students can send an HTTP request from a react application and parse its response to update state.

## Introduction:

In this lesson, we'll practice React with AJAX by learning how to create a React application and use the `fetch()` function to retrieve weather information based on a given city from the [Weather API](https://www.weatherapi.com/).

The application we're building will be completely absent of styles; however, you'll be allowed to style this application later.

## Create Form

In `App.jsx`, let's create a form with which our user can interact:

```jsx
//App.jsx

return (
    <form>
        Please enter your city name for the weather:
        <input type="text"/>
        <input type="submit" value="Get my forecast!" />
    </form>
)
```

## Initializing "city" state variable

Let's add the below import statement to the top of our `App.jsx` component file:

```jsx
// App.jsx
import { useState } from 'react';
```

We need to add state to our main `App` component. We'll achieve this by using the `useState` hook we just imported above to initialize the `city` and `setCity` variables:

```jsx
// import statements above
const App = () => {
    const [city, setCity] = useState('');
    // more code below
```

## 4. Handling the City Change

Now, let's add an event handler to respond to change events when a user types into the text field.

```jsx
const handleChange = (event) => {
  setCity(event.target.value);
}
```

Now update the input to call that function `onChange`:

```jsx
<input type="text" onChange={handleChange} />
```

Lastly, for testing purposes, let's insert the `city` state variable into the DOM somewhere so that we can test to make sure our function works:

```jsx
Please enter your city name for the weather: {city}
```

Now, when you type into the input in the browser, you should see the same value reflected in place of `{city}`. Once you've verified that that works, you can remove `{city}` from the HTML. This was just for testing purposes.

## 5. Fetching the Weather Data

In this section, we're going to make a request to the [Weather API](https://www.weatherapi.com/docs/). This could be done at any point by the application, but let's have JavaScript make the request when the user submits the form.

This API has what is called a key, which you need to access the API. Its function is to deter spammers and hackers by limiting each key's monthly requests. Each application written should use a different API key. Still, for the sake of this exercise, we'll all use the same one (provided below). The free tier is limited to 1 million monthly requests, which will be enough.

If you decide to [sign up](https://www.weatherapi.com/signup.aspx) for a Weather API key, there is a free tier, and no credit card is required!

Let's write a function that will be called when the user submits the form:

```jsx
//App.jsx

const YOUR_API_KEY = '93926e8f19954ff8892185839241302'

const handleSubmit = async (event) => {
    event.preventDefault();
    let response = await fetch(
        `http://api.weatherapi.com/v1/current.json?key=${YOUR_API_KEY}&q=${city}`
    );
    let JSONdata = await response.json();
    console.log(JSONdata)
}
```

Some things to note:

- `YOUR_API_KEY` This holds the key to access the API, which I've already retrieved when writing these notes.
- `event.preventDefault()` This prevents the form from submitting and refreshing the page.
- `fetch()` is a global function with JavaScript that allows us to make AJAX requests to outside sources like the Weather API. Typically, AJAX requests take a long time, so we will prepend the function invocation with `await`. This way, JS will wait until the response comes back from the Weather API before going on to the following line of code. It will return an object containing information about the response from the server, such as header/meta information and the response's body.
- We'll use `response.json()` to return the response's body as a javascript object. This is also an asynchronous event, for reasons you don't have to worry about, so we'll add `await` in front of this too.
- I figured out how to format the URL of the API call by consulting [the docs](https://www.weatherapi.com/docs/). Each API has different request URLs, so an essential aspect of AJAX is parsing through the documentation of a given API. Getting good at this takes practice, so read as much as possible!
- For educational purposes, we've put the API key right here in the file, which is insecure. In production, you would take steps to hide the key from hackers. Check out this module's Securing API Keys lesson to see how that's done.

Now that the function is written let's update the form to call `handleSubmit` when the user submits the form:

```jsx
<form onSubmit={handleSubmit}>
```

Test this in the browser by entering a city and checking the Dev Tools' console. It should log some data about the weather of the location provided.

## Insert Weather Data into the DOM

To get the data we just logged from the Weather API into the DOM, we'll need to create state variables for each piece of data that we want to use. I've chosen three (location, temperature, and conditions), but feel free to add more. Update `App.jsx` as follows:

```js
const App = () => {
    const [city, setCity] = useState('');
    // add the following:
    const [location, setLocation] = useState('');
    const [temperature, setTemperature] = useState('');
    const [conditions, setConditions] = useState('');
```

Once the state variables are set up, we can have `handleSubmit` update those values once retrieved from the API. Update `App.jsx` as follows:

```js
    let JSONdata = await response.json();
    console.log(JSONdata)
    // add the following
    setLocation(JSONdata.location.name)
    setTemperature(JSONdata.current.temp_f)
    setConditions(JSONdata.current.condition.text)
}
```

Rerun this and note the data that is being logged in the console:

![data](https://i.imgur.com/OO1xdpV.png)

Again, each API is different and will have its data formatted differently. As a developer, it's your job to figure out how to access the various data you want to use. In the example, I looked through the object logged in the console to figure out the structure of it.

- I noticed there is a `.location` property on it that itself is an object and contains the `.name` property. I'd like to use that, so `JSONdata.location.name` is what I passed to `setLocation`.
- Similarly, the `.current` property is an object with a property called `.temp_f`, which I pass to `setTemperature`.
- Lastly, `.current` contains an object `.condition`, which includes the `.text` property, which I pass to `setConditions`.

Now that the state variables are set correctly let's update the HTML so the user can see their search results. Update the `return` in `App.jsx` like so:

```js
return (
    <>
      <form onSubmit={handleSubmit}>
        Please enter your city name for the weather:
        <input type="text" onChange={handleChange}/>
        <input type="submit" value="Get my forecast!" />
      </form>
      {/* add the below */}
      <h1> Here's Your Weather Report:</h1>
      <p>Location: {location}</p>
      <p>Temperature: {temperature}</p>
      <p>Conditions: {conditions}</p>
    </>
  );
```

Test it in the browser. Note that there are three stages to this app:

1. Page loads
1. The user enters information and submits the form
1. JavaScript makes a request to the weather API and inserts the data into the DOM without refreshing the page.

The important thing is that the page did not reload after the user submitted the form. AJAX retrieved just the tiny chunk of data needed and then updated a small portion of the page. In the old days, before SPA's and AJAX, this would have required a full page refresh. As you can see, this is a much quicker process.

If it works as expected, you're done. Congratulations!
