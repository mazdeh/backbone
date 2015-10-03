# An Introduction to Backbone.js

For a better layout, head over to the presentation page: http://mazdeh.github.io/backbone/

Rich client-side web applications struggle to keep data in sync between the HTML UI, Javascript logic, and server-side database. 

More often that not, web developers find themselves stuck with piles of jQuery selectors tying the data to the DOM, which in turn, negetively affects performance and overall UX of the WebApp.

Backbone offers a more structured approach to this problem by separating the logic, data, and UI of applications.

# Backbone Terminology — MVC

Backbone has 3 components:
Models
Views
Collections — not controller.

### 1. Models
  * Handles data and business logic.
  * Loads and saves from the server.
  * Fires events upon data change.
   Whenever a UI action causes an attribute of a model to change, the model triggers a ‘change’ event; the **Views** that are watching the state of the model can then be notified of the change, and update themselves accordingly. This eliminates the need to access DOM elements by specific _id_’s, and having to manually change the HTML.

> A **Model** manages an internal table of data attributes, and triggers “change” events when any of its data is modified. Models handle syncing data with a persistence layer — usually a REST API with a backing database.

A good model is one that is re-useable, easily passed around in the code, and a comprehensive representation of the data it is referencing.


### 2. Views
  * Listenfor changes, and render UI.
  * Handle user input and interactivity.
  * Send input to model.

> A View is an atomic chunk of user interface. It often renders the data from a specific model, or number of models — but views can also be data-less chunks of UI that stand alone. Models should be generally unaware of views. Instead, views listen to the model "change" events, and react or re-render themselves appropriately.


### 3. Collections
> A **Collection** helps you deal with a group of related models, handling the loading and saving of new models to the server and providing helper functions for performing aggregations or computations against a list of models. Aside from their own events, collections also proxy through all of the events that occur to models within them, allowing you to listen in one place for any change that might happen to any model in the collection.

# Example Site
In this presentation, I will be building a simple single-page web application with *Backbone.js* to better demonstrate the capabilities of this framework. 
To make the example more interesting, and to avoid the complexities of implementing the server-side, I am going to be using an API instead — Thomas Davis's API. In a later tutorial, I will replace this API with one that we will write together using `express.js` — another great JS library, to introcude some server-side excitement to this project.


#### Set up!
Create an `index.html` file, and include Backbone and its dependancies:
1. jQuery
2. Underscore

Underscore is a great library of amazing, helpful functions. I will cover Underscore in another tutorial later on, but make sure to include it in your `index.html`, or you won't be able to follow along with the example site. 

Let’s get started!

#### Backbone.Router
Backbone is great for single page web applications. The Router watches the URL of your website and allows you to update your site based on the url. Router will also watch for back clicks on the browses, and updates accordingly.

Now, in a `<script>` tag in your `index.html` file, set you home page to be the landing page of your website, like below:

```javascript
var Router = Backbone.Router.extend({
    routes: {
      "": "home"
    }
  });
```

Then, we can instantiate a new Router for our site, by doing:
```javascript
var router = new Router();
```

The Backbone Router class, emits an event called `route`, so we can use jQuery to listen to this event.
Now, `console.log()` and watch your console on the browser, to make sure you router is working properly.
```javascript
router.on('route:home', function () {
	console.log('We're on the Homepage!);
});
```
_note: The Router starts watching for changes only after you run `Backbone.history.start()`. So make sure you have this line towards the end of your `<script>` tag._


#### Backbone.View
Now let's create a Bakcbone view.
Each view has a `render` function that is in charge of rendering the view element.
Keep in mind, that a view shall not reach for anything outside of its own scope. A view is an atomic chunk of UI.

Here's the syntax:
```javascript
var UserListView = Backbone.View.extend({
	el: '.page',
	render: function () {
		this.$el.html('content here');
	}
});
```
We pass in the DOM element to the view, through the View’s  `el` attribute. Then we can access the specific element inside the view by `this.$el`.

_note: Make sure you have a div with class name `page` in your html. That is where the Backbone.View will be rendering itself!_

Now we need to instantiate the view we just created, and render it on our home page.

```javascript
var userListView = new UserListView();
```

Then, we can ask Router to render the userListView for us, on the hompage, by calling userListView’s `render()` function.
```javascript
router.on('route:home', function () {
	userListView.render();
})
```
And that is the basics of how you create a view and render it in the HTML.

#### Backbone.Collection
Collections keep our data. So to populate our users view, we are going to create a Bakcbone.Collection, and point its `url` to a resource endpoint. The Collection will pull from that `url` and store that data.

_note: you need to deinfe the `base url` of your resource endpoint in an ajaxPrefilter function._

```js
var Users = Backbone.Collection.extend({
    url: ‘/users'
});
```
Go ahead and instantiate the Users Collection, inside your userListView’s `render()` function.
```js
var users = new Users();
```

Now we need to fetch down the data from the `/users` datasource to populate the users instance of this collection.

_note: `fetch()` is one of the many Underscore methods that Backbone utilizes. I will be adding more information about Underscore later on. For now, you can just copy the code from `index.html`, where you see `undescrore` comments.

_note: that in fetch's callback function, we do *not* have access to `this`. Hence we need to pass the scope down to the success function by doing:_
```js
var that = this;
```
and then use `that` inside the success callback.

```js
users.fetch({
	success: function() {
		that.$el.html('data came down!');
	}
});
```

Refresh the page, check the *network* tab in your Chrome Dev Tools, and you will see that the data has been successfully fetched!

This concludes our very brief discussion of how to use a RESTful API with Bakcbone's Router, in addition to Backbone's Views and Collections to fetch data from a server and render it in the html.

It should be clear to a somewhat experienced web developer how Backbone's structured approach in separating data and logic from the UI, is making things much easier, and smooth.
---

Next we're going to introduce Backbone Models, and show you how they can make your lives easier, as web devs.

What we've done so far is we've handled a response from a `GET` request from the server. We've parsed the data through our *Collection* class and rendered it using a *View*.

Now, we are going to see how we can use Routers and Models to do some other HTTP requests (i.e POST, PUT, DELETE).

_note: to be able to continue on using Thomas's server, we are going to implement a simple add/edit user functionality to the current state of our app. This will however change as we move onto implementing our own server, using `express.js`._


Make a button somewhere inside the Underscore template (`user-list-template`). The `href` should point to `#/new`.

We're using a `#` because this is a single-page application, we do not want to navigate to a different page. Backbone removes the hashtag from the URL. 

Now we need to let the Router know that we have another URL that it should be watching for. So let's go ahead and add that to the Router class:

```js
var Router = Backbone.Router.extend({
    routes: {
      "": "home",
      "new": "editUser"
    }
});
```
And tell the router to do the right thing, when it observes the `/new` URL.
```js
router.on('route:editUser', function() {
  console.log('I know what do, when New is clicked.');
});
```
So, as you may have guessed, we need a new View here, so that when the New button is clicked we show the user and appropriate view, where they can add their user information. So let's create a Backbone View that does just that :
```js
var EditUserView = Backbone.View.extend({
  el: '.page',
  render: function () {
    this.$el.html('We're on /new!');
  }
});
```
Note that we are passing the same `el` to this view, because we would want it to replace the current element of the DOM with the new one that we will be providing it with.

As you should have guessed by now, we are going to instantiate the new view, and update our router listener to render the view when it oberves the new URL.

Now let's make that `editUserView` show something more meaningful. Just add a new Underscore template with the `id="edit-user-template"` and update the view so that it populates the `html` with the template you just created. In you template, it would makes sense to have some sort of a form, where you'd ask for things like firstname, lastname, and age. You will also need a Submit button, so we can actually do a `POST` to server.

_note: the templates in this example are all made using Underscore's templating, which I will cover later on! You could however use any templating tool you prefere, like Handlebars, etc._

The updated view should look something like this:
```js
var EditUserView = Backbone.View.extend({
  el: '.page',
  render: function () {
    var template = _.template($('#edit-user-template').html(), { });
    this.$el.html(template);
  }
});
```

Now if you click on the New button, it should take you to the Create New User page, and show you the form you created in the template.

##### Backbone Events

We need a way to listen to the form's submit button, so that we take the appropriate action to create a new user, when the button is clicked. Backbone Views have a events object, that listens for different events that happen on the specified DOM elements.

Here's the syntax for how you'd create events inside a Backbone View:

```js
events: {
  'event selector': 'action'
}
```
`event` could be any jQuery event (i.e. click, focus, etc.).
We would like to listen to the submit event of the `.edit-user-template` form, and then we want to fire a function that handles a `POST` to the server. So:
```js
events: {
  'submit .edit-user-template': 'saveUser'
}
```
The event passes an `ev` object to `saveUser` that gives us access to that specific event.

Now that we are listenting to this event, let's define our `saveUser` function inside the View.
Fist, we need to grab the form elements and trun it into a JSON object. You can find a jQuery `serizlizeObject()` function online that does that for you. 
```js
saveUser: function (ev) {
  var userDetails = $(ev.currentTarget).serializeObject();
}
```
Now that we have a JSON object (model) of the data, let's turn it into a Backbone Model.

#### Backbone.Model
Models are the units in a Collection. So where the Collection is an array of object (models), the Model is the actual object. So we are going to call the Model `User` rather than `Users`, that we used for the Collection.

The syntax that should look familiar to you by now:
```js
var User = Backbone.Model.extend({
    urlRoot: '/users'
});
```
`urlRoot` is pretty similar to the `url` attribute that you saw in Collections. The Model class however knows how to append to the `urlRoot` based on the request it gets. So if we did a `PUT` requests, it appends the `id` to the end of the `urlRoot`. Similarly for `DELETE`. But if we were to do a `POST` to `/users`, it would know that there doesn't need to be an `id` attached to the `urlRoot`. 

Now that we have a Model, we want the `saveUser` function to save this model to the server for us. So in `saveUser` we add:
```js
var user = new User(); // instantiate the Model
user.save(userDetails, {
  success: function() {
    // do something
  }
});
return false; // this line is required to finish the request!
```
As you can see, the `save()` function, takes in as its first argument, the model object, and as its second argument an options hash, where you could tell it what to do upon `success` or `error`. 

`save()` is intelligent enough to know what request to send to the server. So if you check your network tab, after you create a new user, you should see a `POST` request.

But what if this user already existed in our database, and we only wanted to do a `PUT` request to update it?
Let's modify our code, just a little bit, to account for that case.

First, you should add an *edit* button to your `edit-user-template`, for each user in the table. We need to include the `id` of the user that we're trying to edit in the `href` of the edit button. So when then Edit button is clicked, our URL should change to something like: `#/edit/user.id`

_note: Don't worry, passing in the `user.id` throught the HTML is Underscore stuff that I will cover later!_

Now we should update our Router to watch for the `/edit` URL.
```js
var Router = Backbone.Router.extend({
  routes: {
    "": "home",
    "new": "editUser",
    "edit/:id": "editUser"
  }
});
```

Notice that the Router will direct both `new` and `edit/:id` to the same editUser's callback. So we need to modify our callback to account for when there's an `id` passed along. This means our `router` instance will have an `id` passed on to it.
```js
router.on('route:editUser', function(id) { // id is being passed to the callback function
  editUserView.render(id);
});
```
So now we can pass that `id` to our `render()` function and modify our `EditUserView` to account for that.
This is simply done by adding an `options` variable to our `render()` function inside `EditUserView`, and then adding some conditionals to see if `id` is present or not. If we have an `options.id`, we sould want to fetch that user from the server and populate the form with that user's info, and if no id is present, we will just have an empty form, so that the a new user's info could be entered.

Here's how our `render()` function would look like after these changes:
```js
render: function (options) {
  if (options.id) {
    var user = new User({id: options.id});
    var that = this;
    user.fetch({
      success: function(user) {
        var template = _.template($('#edit-user-template').html(), { user: user });  // pass in the user model
        that.$el.html(template);  // populated form
      }
    })
  } else {
    var template = _.template($('#edit-user-template').html(), { user: null });   // no id present, no user model
    this.$el.html(template);  // empty form
  }
}
```

Now we need to make sure our form gets populated with the user's data. This is more Underscore stuff that I will be covering later!

We are now able to update a user on the server, by sending a `PUT` request. As we already talked about `user.save()` knows what requet to send to the server based on if there's an `id` or not.

---
This concludes our introduction to Backbone.js. This tutorial was inspired by Thomas Davis's. I will update this project with more information on Underscore, and then I will be creating a server-side component for it, using Express. Stay tuned!







