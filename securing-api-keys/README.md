# ![Fetching Data in React - Securing API Keys](./assets/hero.png)

**Learning objective:** By the end of this lesson, students will be able to create a proxy server that will hide their API key

## Description

API keys are used as a means of authentication and authorization when accessing APIs. The primary purpose of API keys is to provide a secure way for developers and applications to interact with APIs while controlling access and monitoring usage. Because API keys are a means of security, it is important to put API keys in a secure location when building your apps.

Imagine you are working on application and you leave your API key visible in your code. If you were to push your code to GitHub, someone could find your repo and copy your API key so they can make requests using it. This would then make you responsible for the calls that this person made.  If an API is using a key to limit requests per day, this other person might reach your allotted number of requests. This means your app wouldn't be able to make any more requests to get the data that it needs to run properly.  Even worse, if the API key is tied to a credit card that is charged for each request made, you could end up owing a lot of money.  Even if you don't push your code to GitHub, if your app is deployed, there are other ways hackers could figure out your API Key.

API key security is essential, and in this lesson we're going to go over how to make sure your API keys are securely hidden from hackers.

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

Notice that there's no API key in the 2nd request.  Your React code can be pushed to GitHub without giving away your API key.  As you'll see in this lesson, we'll use a `.env` file to store the API key in our proxy server.  We'll then tell Git to ignore the `.env` file, using a `.gitignore` file.  Once this is complete, our proxy server code will be safe to push to GitHub as well.

## Initialize the Proxy Server App

**NOTE:** Have your Fetching Data in React Weather Application open and your dev server running.

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

> Note: In the code above there is a response coming back from the api AND a response that your Express app will send to your React app. Make sure your variables are named appropriately so that you can differentiate them.

If you go to `http://localhost:3000/` in the browser, you should see the data that your proxy server retrieves from the API.

## Adjust the `fetch` URL

You might notice that I've hard-coded the city that we're querying as `Philadelphia`.  This is just a random choice that I made for the purposes of testing.  Of course we don't want to only query for `Philadelphia`.  Let's make it so that the user can specify any city by assigning a value to a query param called `city`:

```js
const apiResponse = await fetch(`http://api.weatherapi.com/v1/current.json?key=93926e8f19954ff8892185839241302&q=${req.query.city}`)
```

Now we are able to get results for any city that we query. `http://localhost:3000/?city=Raleigh` will search for  Raleigh, `http://localhost:3000/?city=Philadelphia`will search for Philadelphia, and so on.

## Change React AJAX URL

Now change the URL your React app makes a request to (you'll go back to your Weather Application code for this):

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

Be sure to delete the following line of code from your React app:

```js
const YOUR_API_KEY = '93926e8f19954ff8892185839241302'
```

Now your React code is nice and secure!  

But wait!  If you test this out, the React app should break. You should see errors in the console about how your AJAX request was blocked by "CORS policy" with an error message that looks something like this:

![](https://i.imgur.com/gj5QNtA.png)

Don't worry!  We'll solve that next.

## Set Up CORS

Note that your React app is running on port `5173` and your new proxy server is running on port `3000`.  This is good!  Each port can only have one application listening on it.  The problem is that it's considered insecure for browsers to make AJAX requests to other domains or ports (this is called Cross Origin Resource Sharing or CORS).  So even though both React and your proxy server are accessed via `localhost`, because they're on different ports, it's considered insecure to have your React app make an AJAX request to your proxy server.  This is easily fixed, though!

Install the `cors` NPM package for your proxy server:

```bash
npm install cors
```

Now `require` it in your express app:

```js
const cors = require('cors');
```

Because the `cors` package is an express middleware package, let's `use` by putting it above our `/` route handler:

```js
app.use(cors())
```

Doing this tells our Express app to allow requests from other domains and ports.  Test your React app, and you should see now that it works again.  Yay!

## Hide the API Key

We're almsot at the end! The next step is hiding our API key, which is still visible in our Express code.  Not secure!  Let's hide it in a `.env` file.  First, install the `dotenv` package:

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

And now create an environment variable inside our `.env` file and paste the key from your Express app:

```bash
API_KEY=93926e8f19954ff8892185839241302
```

Now you can use `process.env.API_KEY` in your Express app to place the API key inside the `fetch` request like so:

```js
const apiResponse = await fetch(`http://api.weatherapi.com/v1/current.json?key=${process.env.API_KEY}&q=${req.query.city}`)
```

Your React app should work normally if you test it.

## Ignore the `.env` file

Now that your API key is in the `.env` file, we should make sure that Git doesn't track that file and make the contents of it available for others to see.  If your Express app directory isn't already a Git repo, we can initialize it like so:

```bash
git init
```

If we run `git status`, we should see something like the following:

![](https://i.imgur.com/udEfHbJ.png)

Create the `.gitignore` file by running the following:

```bash
touch .gitignore
```

Now edit `.gitignore` to ignore the `.env` file (and while we're in there, we might as well ignore the `node_modules` directory as well - this keeps repository sizes manageable and our Git operations efficient):

```bash
.env
node_modules
```

Now when you run `git status` you should see the following:

![](https://i.imgur.com/ZELzZFA.png)

That's it!  Now your API key is securely hidden.