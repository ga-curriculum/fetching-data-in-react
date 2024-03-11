# ![Fetching Data in React - Concepts](./assets/hero.png)

**Learning objective:** By the end of this lesson, students will be able to describe what AJAX is and its role in Single Page Applications.

## Retrieving data in React

Data can flow within a React app through `props`, but what happens when we want to **retrieve data from outside** of our application? In order to build a full-stack React app, we need a method for communicating with external resources, like a server. This is where AJAX comes into play.

## What is AJAX?

The term **AJAX (Asynchronous JavaScript and XML)** refers to a collection of techniques for sending and receiving data within client-side web applications. These techniques allow client-side apps to communicate asynchronously with a server, and update the DOM accordingly. Ultimately, this produces more interactive web applications. 

Despite the inclusion of XML in its name, today, AJAX typically deals with the exchange of JSON (JavaScript Object Notation) data. It's important to note that this exchange of data is asynchronous, meaning data can be fetched without freezing the user inteface, or requiring a page reload. In modern web apps, this asynchronous exchange of data is often achieved through JavaScript's **Fetch API** or libraries such as Axios.

> 🧠 AJAX originally relied on the XMLHttpRequest object, but this approach has largely been replaced by tools like the Fetch API or Axios.

## The role of AJAX in single-page applications

Prior to the introduction of Single-Page Applications, developers created Server-Side Rendered Applications that injected data into templates. Picture a site like Amazon for example. There is a single template for the 'Product' page, and then data is inserted into that template to render each product on the site. This works quite well, but the downside is that each user interaction (like clicking on a new product), requires a brand new page to be loaded. This can often be time consuming.

The advantage to Single Page Applications is that they only load tiny pieces of data in response to user interactions, then use that data to update the DOM. 

The most famous example of this is Google Maps. Prior to Google Maps, MapQuest was a Server-Side Rendered Mapping Application. It would show you a map, and if you wanted to move the map east, for example, you would click a button and a brand new page would load with the updated map. If you wanted to move east quite a bit, this would require several page loads (a very frustrating user experience). Google Maps however allows users to drag the map, which then updates the map immediately without the need for a brand new page load. 

It accomplishes this with JavaScript. The application waits for a user drag interaction, and then makes an AJAX request to Google's server. The server responds with just the data needed to update the map, and the application updates the DOM using that data.

## AJAX in React

In this module, we will be using `fetch()` within our React apps to request data from external sources. Oftentimes, these external sources will be **Web API's**.

In the context of web development, a **Web API (Application Programming Interface)** refers to a web service that allows clients to communicate with a server. While traditional websites might respond to requests with HTML, Web API's often respond to HTTP requests with something like JSON data. This allows the information to be consumed by other applications.

With AJAX techniques like `fetch()`, our React apps will be able to make requests to an API, and then update the DOM using the data sent as a response. 

> 💡 With a React app, we can fetch the data from anywhere. Unlike Server-Side Rendered Applications, there is no inherent connection between the React applications that we will write and the data sources that we will be working with.