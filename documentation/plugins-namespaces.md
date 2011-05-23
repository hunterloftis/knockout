---
layout: documentation
title: Namespaces
---

Most Knockout-based apps will also dynamically fetch data, add it to a view model, and bind to a view. By itself, Knockout supports only a single global view model - if you want multiple (eg ajaxed data), you must break bindings into specific DOM containers, or use the namespaces plugin.

Namespaces provide an elegant solution for multiple view-models. They will bind 'data-bind,' 'data-bind-someSpace,' and 'data-bind-anotherSpace' without conflict and without DOM wrappers.

{% capture plugin_download_link %}
 * __[Version 0.1.0](https://github.com/hunterloftis/knockout.namespaces/tree/master/build/output)__ (1.5kb minified)
{% endcapture %}
{% include plugin-download-link.html %}

### Github

[Knockout.namespaces on github](https://github.com/hunterloftis/knockout.namespaces) is maintained by Hunter Loftis.
Contributions are welcome, especially to the weak areas of templating and tests.

### Example: Binging 'user' without the ko.namespaces plugin

You want to display information about the logged-in user at several places in your app. The information is represented using the following view model:

  var userViewModel = {
    name: ko.observable(),
    role: ko.observable(),
    friends: ko.observableArray()
  }

You could bind this view model to multiple places as follows:

  <div id='userStatus'>
    Signed in as <span data-bind='text: name'></span>. Not you? <a href='/signout'>Sign out.</a>
  </div>
  
  <div id='deleteProject' data-bind='visible: (role() === "admin")'>
    <a href='/delete'>Delete the project</a>
  </div>
  
  <div id='friendList' data-bind='template: {name: "friendLink", foreach: friends}'></div>

To bind your view model to these views, you would write:

  <script>
    ko.applyBindings(userViewModel, 'userStatus');
    ko.applyBindings(userViewModel, 'deleteProject');
    ko.applyBindings(userViewModel, 'friendList');
  </script>
	
You would have to applyBindings() for every view bound to userViewModel - so you have to know all of your views, and their IDs, in advance. You also couldn't do this:

  var projectViewModel = {
    title: ko.observable()
  }
  
  <div id='deleteProject' data-bind='visible: (role() === "admin"'>
    <a href='/delete'>Delete the project '<span data-bind='text: title'></span>'</a>
  </div>
  
Why not? Because you want 'text: title' to refer to projectViewModel, but this DOM tree has previously been bound to userViewModel, which doesn't contain a member called 'title' and will throw an exception trying to bind it.

### Example: Using ko.namespaces

First, you can get rid of wrappers and IDs. Then, add -namespaces to your data-bind attributes:

  Signed in as <span data-bind-user='text: name'></span>. Not you? <a href='/signout'>Sign out.</a>
  
  <div data-bind-user='visible: (role() === "admin"'>
    <a href='/delete'>Delete the project '<span data-bind-project='text: title'></span>'</a>
  </div>
  
  <div id='friendList' data-bind-user='template: {name: "friendLink", foreach: friends}'></div>

Now you can use one namespaced binding per view model:

  ko.applyBindings(userViewModel, 'user');
  ko.applyBindings(userViewModel, 'project');
  
Also, you can still use the global (default) namespace with no conflicts:

  ko.applyBindings(globalViewModel);

Using namespaces maintains the MVVM pattern (view models don't know anything about views), minimizes code complexity, and allows multiple view models to coexist in harmony.

{% include plugin-download-link.html %}