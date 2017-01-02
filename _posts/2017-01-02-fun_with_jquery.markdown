---
layout: post
title:  "Fun with JQuery"
date:   2017-01-02 21:14:09 +0000
---


In this post, I'll be talking about the process to replace the commenting feature in my [last project](https://github.com/KerryAlsace/where-to-work) with a better, jquery-based one. You can read a brief summary of my misadventures from that project [here](https://kerryalsace.github.io/2016/11/05/developing_with_user_requirements_in_mind/), but for a more technical history of that project to get us up to speed, here's a quick step-by-step:

Create the new rails app, enter its folder, initialize git, and make the initial commit:
```
$ rails new where-to-work
$ cd where-to-work/
$ git init
$ git add .
$ git commit -m "initial commit"
```

Next, I created the models and controllers, with a ton (perhaps too many in retrospect) of relationships. To keep it clear for our purposes, here are the relationships that are relevant to this post:

```
Place
belongs_to :user
has_many :comments
```

```
Comment
belongs_to :place
```

```
User
has_many :places
```

I ended up abandoning the `Comment` model and replacing it with a `comments` and a `add_comment_to_place` function in the `Place` model. That method was a bit long winded, and required the page to reload to display the new comment among the existing comments.

Let's replace that with JQuery!

Now that we've brought back the `Comment` model, we can add a feature to show a place's comments on the `Place` show page (places/show.html.erb).

First, we'll need to define `@comment` in places_controller.rb in the `show` method. Here we can just add
```
@comment = Comment.new
```

Then, we can go back to places/show.html.erb and add a small form at the bottom:

```
Add a comment:<br>
<%= form_for(@comment, url: place_comments_path(@place)) do |f| %>
  <%= f.text_area :body %>
  <p><%= f.submit "Add", class: "add_comment" %></p>
<% end %>
```

You'll notice that we include `url: place_comments_path(@place)`. That's so that our form will know that this isn't just a random comment to add into the ether of our database, but that it belongs to `@place`, which is the place whose page we're currently on.

Now we get to add in some javascript to make things work. Note: If you're using Rails 5, I'd recommend getting rid of everything `turbolinks`. I'd heard it didn't work well with Rails 5, but after an hour of unsuccessful debugging led me to a StackOverflow answer saying to remove `turbolinks`, I now understand. You'll want to remove 

```
# Turbolinks makes navigating your web application faster. Read more: https://github.com/turbolinks/turbolinks
gem 'turbolinks', '~> 5'
```
from your Gemfile,

```
//= require turbolinks
```

from app/assets/javascripts/application.js, 

and change
```
<%= stylesheet_link_tag    'application', media: 'all', 'data-turbolinks-track': 'reload' %>
<%= javascript_include_tag 'application', 'data-turbolinks-track': 'reload' %>
```

to

```
<%= stylesheet_link_tag    'application', media: 'all' %>
<%= javascript_include_tag 'application' %>
```

in app/views/layouts/application.html.erb.

First, let's create an app/assets/javascripts/places.js file. In that let's add a function that will initiate once the page is finished loading:

```
$(document).ready(function() {
   // Our code will go here
});
```

Feel free to add a debugger or an `alert('Hey there!')` inside that function to test that it's being called when the page loads.

Now we can start building out our function for creating a comment when someone clicks 'Add' under their new comment.

```
// When a user adds a comment and clicks to submit
  $("#new_comment").on("submit", function(e){

    $.ajax({
      type: "POST",
      url: this.action,
      data: $(this).serialize(),
      success: function(response){
        console.log(response);
      }
    });

    e.preventDefault();
  });
```

What we're doing here is adding a submit event to the new_comment form. What this means is that when a user clicks to submit their comment, this function will fire. We add `e.preventDefault();` because normally, a form submission causes the page to reload, but we don't want that to happen.

The bulk of this function is the Ajax call. Since we're posting data, we use `type: "POST"`, then we use a little bit of `this` magic to grab the url to post to, and serialize the comment data to send with that post request. If you check your console, you'll see that the response is basically a copy of the entire html of the page. Since we only want the portion that holds the comment body that was just added, we'll need to tweak it a little bit.

First, let's add a app/views/comments/show.html.erb file. In it, let's just have a simple `li` format for the comment:
```
<li><%= @comment.body %></li>
```

Then, let's go to our comments controller and add:

```
def create
	@comment = @place.comments.build(comments_params)
	if @comment.save
		render "comments/show", layout: false
	else
		redirect_to "posts/show"
	end
end

private

	def comments_params
		params.require(:comment).permit(:body)
	end
```

Now when 'comments#create' is called by `place_comments_path(@place)`, it will hit this method and render the comment in our new show page.

Lastly, we want to actually display all our comments on the place page so that we can see them after we add new ones.

Let's add

```
<%= link_to "Click to Show Comments", place_comments_path(@place), class: "load_comments" %><br>
<ol class="comments"></ol>
```

in our places show page. This will add a button that we can bind a click event to which will load all the comments and display them on that page.

Back in places.js, let's add 

```
// When a user clicks to load comments
  $("a.load_comments").on("click", function(e){

    // Send AJAX get request and if successful:
    $.get(this.href).success(function(json){
      var $ol = $("ol.comments")
      $ol.html("") // Clear the comments div first

      // Add comments to DOM
      json.forEach(function(comment){
        $ol.append("<li>" + comment.body + "</li>");
      });
    });

    // Stop from doing the normal redirect action
    e.preventDefault();
  });
```

within our document ready function.

Lastly, let's define comments in the places_controller.rb. Within the `show` method, add:

```
@comments = @place.comments
```

Great! Now we should be able to add a comment to a place and see it pop up when we click to show all the places comments!
