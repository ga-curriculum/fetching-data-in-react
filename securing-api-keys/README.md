# ![Fetching Data in React - Securing API Keys](./assets/hero.png)

**Learning objective:** By the end of this lesson, students will be able to create a proxy server that will hide their API key

## Description

As previously mentioned in this module, putting API keys in your code is insecure.  If you were to push your code to GitHub, someone could find your repo and copy your API key so they can make requests using it.  You would then be responsible for the calls that this person made.  If an API is using a key to limit requests per day, this other person might reach your allotted number of requests and then your app would not be able to make any more requests.  Even worse, if the API key is tied to a credit card that is charged for each request made, you could end up owing a lot of money.  Even if you don't push your code to GitHub, if your app is deployed, there are other ways hackers could figure out your API Key.

In this lesson we're going to go over how to make sure your API keys are securely hidden from hackers.

## Overview

The easiest way to hide your API keys is to create a "proxy server," which is basically just a web server application which itself makes requests to the API.  Here's how the flow of requests/responses would go:

1. Your React application makes a request to the "proxy server" you've created
1. The proxy server in turn makes a request to the API
1. The API responds with data to your proxy server
1. Your proxy server responds to your React application with the data it just received from the API.

![tktk Hunter, something like this, but not so awful, please](https://i.imgur.com/NXN2RnD.png)

Why is this helpful?  Because we can use a `.env` file to hide the API key when writing our proxy server app.  This way, instead of having your React app make requests to:

```
http://api.weatherapi.com/v1/current.json?key=93926e8f19954ff8892185839241302&q=Philadelphia
```

Your React app would make a request to:

```
http://localhost:3000/?city=Philadelphia
```

Notice that there's no API key in the 2nd request.  Your React code can be pushed to GitHub without giving away your API key.  As we'll see in this lesson, we'll use a `.env` file to store the API key in our proxy server.  We'll then tell Git to ignore the `.env` file, using a `.gitignore` file.  Once this is complete, our proxy server code will be safe to push to GitHub as well.

## Initialize the Proxy Server App

**NOTE:** Keep your old React code open and your dev server running.  We'll return to it shortly.

Go into  **`~/code/ga/lectures`** and create a new directory called `proxy-server`:

```bash
cd ~/code/ga/lectures
mkdir proxy-server
```

`cd` into that directory and create a `server.js` file:

```bash
cd proxy-server
touch server.js
```

Now initialize a Node.js project and install express

```bash
npm init -y
npm install express
```

In `server.js`, initialize a new Express app:

```js
const express = require('express');
const app = express();

app.listen(3000, ()=>{
	console.log('listening on port 3000...')
})
```

Start it by running `nodemon` in the terminal and test out `http://localhost:3000/` in the browser.  You should see "Cannot GET /"

## Create Proxy Route

Now let's create a route that our React app will make requests to:

```js
app.get('/', async (req, res)=>{
	const apiResponse = await fetch(`http://api.weatherapi.com/v1/current.json?key=93926e8f19954ff8892185839241302&q=Philadelphia`)
	const data = await apiResponse.json()
	res.json(data)
});
```

It's important to keep in mind that there is a response coming back from the api and a response that your Express app send to your React app.  Make sure your variables are named appropriately so that you can differentiate them.

If you go to `http://localhost:3000/` in the browser, you should see the data that your proxy server retrieves from the API.

## Adjust the `fetch` URL

You might notice that I've hard-coded the city that we're querying as `Philadelphia`.  This is just a random choice that I made for the purposes of testing.  Of course we don't want to only query for `Philadelphia`.  Let's make it so that the user can specify any city by assigning a value to a query param called `city`:

```js
const apiResponse = await fetch(`http://api.weatherapi.com/v1/current.json?key=93926e8f19954ff8892185839241302&q=${req.query.city}`)
```

Now `http://localhost:3000/?city=Raleigh` will search for  Raleigh, and of course you can change the value of `city` to whatever you want.

## Change React AJAX URL

Now change the URL your React app makes a request to:

```js
const handleSubmit = async (event) => {
	event.preventDefault();
	let response = await fetch(
		`http://localhost:3000/?city=${city}`
	);
	let JSONdata = await response.json();
	console.log(JSONdata)
}
```

Also, be sure to delete the following line of code from your React app:

```js
const YOUR_API_KEY = '93926e8f19954ff8892185839241302'
```

Now your React code is nice and secure!  But wait!  If you test this out, the React app should break, and you should see errors in the console about how your AJAX request was blocked by "CORS policy."  Something like this:

![](https://i.imgur.com/gj5QNtA.png)

Don't worry!  We'll solve that next.

## Set Up CORS

Note that your React app is running on port `5173` and your new proxy server is running on port `3000`.  This is good!  Each port can only have on application listening on it.  The problem is that it's considered insecure for browsers to make AJAX requests to other domains or ports (this is called Cross Origin Resource Sharing or CORS).  So even though both React and your proxy server are accessed via `localhost`, because they're on different ports, it's considered insecure to have your React app make an AJAX request to your proxy server.  This is easily fixed, though!

Install the `cors` NPM package for your proxy server:

```bash
npm install cors
```

Now `require` it in your express app:

```js
const cors = require('cors');
```

The `cors` package is an express middleware package, so let's `use` it (remember to put this above your `/` route handler):

```js
app.use(cors())
```

Doing this tells our Express app to allow requests from other domains and ports.  Test your React app, and you should see now that it works again.  Yay!

## Hide the API Key

We're almost done, but our API key is still in our Express code.  Not secure!  Let's hide it in a `.env` file.  First, install the `dotenv` package:

```bash
npm i dotenv
```

Now initialize it at the top of our Express app:

```js
require('dotenv').config();
```

Create the `.env` file:

```bash
touch .env
```

And now create an environment variable and paste the key from your Express app:

```bash
API_KEY=93926e8f19954ff8892185839241302
```

Finally, you can use `process.env.API_KEY` in your Express app to place the environment variable inside the `fetch` request like so:

```js
const apiResponse = await fetch(`http://api.weatherapi.com/v1/current.json?key=${process.env.API_KEY}&q=${req.query.city}`)
```

Your React app should work normally if you test it.

## Ignore the `.env` file

Now that your API key is in the `.env` file, we should make sure that Git doesn't track that file.  If your Express app directory, isn't already a Git repo, we can initialize it like so:

```bash
git init
```

If we run `git status`, we should see something like the following:

![](https://i.imgur.com/udEfHbJ.png)

Create the `.gitignore` file by running the following:

```bash
touch .gitignore
```

Now edit `.gitignore` to ignore the `.env` file (and while we're in there, we might as well ignore the `node_modules` directory as well):

```bash
.env
node_modules
```

Now when you run `git status` you should see the following:

![](https://i.imgur.com/ZELzZFA.png)

That's it!  Now your API key is securely hidden.