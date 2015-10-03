# An Introduction to Backbone.js

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


In this presentation, I will building a simple User Manager site with *Backbone.js* to demonstrate the capabilities of this framework. To make the example more interesting, and to avoid going through implementation process of a server-side backend, we're going to use an API.


# 1. Set up Backbone.js for your site
Create an index.html file, and include Backbone and its dependancies:
1. jQuery
2. Underscore

Underscore is a great library of amazing, helpful functions. I will cover Underscore in another tutorial later on, but make sure to include it in your `index.html`, or you won't be able to follow along with the example site. 


# 2. Backbone.Router
Backbone is great for single page web applications. The Router watches the URL of your website and allows you to update your site based on the url.

For example, in the code below, we're telling Bakcbone.Router to set our `home` as our landing page.

```javascript
var Router = Backbone.Router.extend({
    routes: {
      "": "home"
    }
  });
```

Then, we can instantiate a new Router for the our site, by doing:
```javascript
var router = new Router();
```

`console.log()` something to make sure your Router is working:
```javascript
router.on('route:home', function () {
	console.log('We're on the Homepage!);
});
```

note: The Router starts watching for changes only after you run `Backbone.hustory.start()`.


# 3. Backbone.View
Now let's create a Bakcbone view.
Each view has a `render` function that is in charge of rendering the view element.
Keep in mind, that a view shall not reach for anything outside of its own scope.


Here's the syntax:

```javascript
var UserListView = Backbone.View.extend({
	el: '.page',
	render: function () {
		this.$el.html('content here');
	}	
});
```

We pass in the DOM element to the view, through the `el` attribute. Then we can access the specific element inside the view by `this.$el`.

note: make sure you have a div with class name `page` in your html. That is where the Backbone.View will be rendering itself.

Now we need to instantiate this view, and render it on our home page. In 

```javascript
var userListView = new UserListView();

```

Then, we can ask Router to render the userListView for us, on the hompage, by calling the `render()` function in our `UserListView()` view.
```javascript
router.on('route:home', function () {
	userListView.render();
})
```


# 4. Bakcbone.Collections
Collections keep our data. So to populate our users view, we are going to create a Bakcbone.Collection, and point its `url` to a resource endpoint.

note: you need to deinfe the base url of your resource endpoint in an ajaxPrefilter function.

```js
var Users = Backbone.Collection.extend({
    url: '/users'
  });
```

Go ahead and instantiate the Users Collection, inside your view's `render()` function.

```js
var users = new Users();
```


Now we need to fetch down the data from the `/users` datasource to populate the users instance of this collection. Note that fetch is one of the many Underscore methods that Backbone utilizes.

Also note that in fetch's callback function, we do not have access to `this`. Hence we need to pass the scope down to the success function by doing:
```js
var that = this;
```
and then using `that` inside the success callback.

```js
users.fetch({
	success: function() {
		that.$el.html('data came down!');
	}
});
```

Refresh the page, and you will see that the data has been successfully fetched.







### API intergration
We can simply create Collections of data by setting the value of the `url` attribute to our resource endpoint. (i.e. a router call to the database or an API call)

var Users = Backbone









