# ![React Data Fetching - Concepts](./assets/hero.png)

**Learning objective:** By the end of this lesson, students will be able to describe what AJAX is as well as explain why it is useful within the context of Single Page Applications

## Description

AJAX stands for Asynchronous JavaScript and XML.  It's not necessary to remember that, but what's important to remember is that after page loads in the browser window, JavaScript has the ability to make a request to a server for some data.  Once the response comes back from the server, JavaScript can then use that data to update the DOM.

One quick thing to note is that originally, AJAX used XML data that looked something like this:

```xml
<dog>
	<name>Bruno</name>
	<age>5</age>
</dog>
```

This looks a lot like HTML but with custom tag names.  The major downside to this format is that the size of the data was kind of large.  The industry switched to JSON later on, which is more concise and looks like this:

```json
{
	"name":"Bruno",
	"age":5
}
```

## Why is it useful?

Prior to the introduction of Single Page Applications, developers created Server-Side Rendered Applications that injected data into templates.  If you think of Amazon or Etsy, these are good examples.  There is a single template for the "Product" page, and then data is inserted into that template to render each product on the site.  This works quite well, but the downside is that each user interaction, for instance clicking on a new product, requires a brand new page to be loaded.  This can be time consuming, based on the size of the page.

The advantage to Single Page Applications is that they only load tiny pieces of data in response to user interactions, then use that data to update the DOM.  The most famous example of this is Google Maps.  Prior to Google Maps, MapQuest was a Server-Side Rendered Mapping Application.  It would show you a map, and if you wanted to move the map east, for example, you would click a button and a brand new page would load with the updated map.  If you wanted to move east quite a bit, this would require several page loads (a very frustrating user experience).  Google Maps however allows users to drag the map, which then updates the map immediately without the need for a brand new page load.  The way it does this is that the application's JavaScript waits for a user drag interaction and then makes an AJAX request to Google's server.  The server responds with just the data needed to update the map, and the application updates the DOM using that data.

## How will we work with AJAX in React?

In this module, we will be using AJAX to fetch data from an external source called an API and then update the DOM using that data.  An API is a website that responds with JSON data instead of HTML/CSS/JS.  Note, that we could fetch the data from anywhere.  Unlike in Server-Side Rendered Applications, there is no inherent connection between the React applications that we will write and the data sources that we will be working with.

