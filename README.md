Backbone JS

# Introduction

Rich client-side web applications struggle to keep data in sync between the HTML UI, Javascript logic, and server-side database. 

More often that not, web developers find themselves stuck with piles of jQuery selectors tying the data to the DOM, which in turn, negetively affects performance and overall UX of the WebApp.

Backbone offers a more structured approach to this problem by separating the logic, data, and UI of applications.

# Backbone Terminology — MVC

Backbone has 3 components:
Models
Views
Collections — not controller.

## 1. Models
..* Handles data and business logic.
..* Loads and saves from the server.
..* Fires events upon data change.
...Whenever a UI action causes an attribute of a model to change, the model triggers a ‘change’ event; the **Views** that are watching the state of the model can then be notified of the change, and update themselves accordingly. This eliminates the need to access DOM elements by specific _id_’s, and having to manually change the HTML.

> A **Model** manages an internal table of data attributes, and triggers “change” events when any of its data is modified. Models handle syncing data with a persistence layer — usually a REST API with a backing database.

A good model is one that is re-useable, easily passed around in the code, and a comprehensive representation of the data it is referencing.


## 2. Views
..* Listenfor changes, and render UI.
..* Handle user input and interactivity.
..* Send input to model.

> A View is an atomic chunk of user interface. It often renders the data from a specific model, or number of models — but views can also be data-less chunks of UI that stand alone. Models should be generally unaware of views. Instead, views listen to the model "change" events, and react or re-render themselves appropriately.


## 3. Collections
> A **Collection** helps you deal with a group of related models, handling the loading and saving of new models to the server and providing helper functions for performing aggregations or computations against a list of models. Aside from their own events, collections also proxy through all of the events that occur to models within them, allowing you to listen in one place for any change that might happen to any model in the collection.


In this presentation, I will building a simple User Manager site with *Backbone.js* to demonstrate the capabilities of this framework. 


# 1. Set up Backbone.js for your site
Create an index.html file, and include Backbone and its dependancies:
#### 1. jQuery
#### 2. Underscore


### Backbone.Router
Backbone is great for single page web applications. The Router watches the URL of your website and allows you to update your site based on the url.

```javascript
var Router = Backbone.Router.extend({
    routes: {
      "": "home"
    }
  });




### API intergration
We can simply create Collections of data by setting the value of the `url` attribute to our resource endpoint. (i.e. a router call to the database or an API call)

var Users = Backbone









