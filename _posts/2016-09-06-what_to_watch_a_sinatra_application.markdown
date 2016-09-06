---
layout: post
title:  "What To Watch: A Sinatra Application"
date:   2016-09-06 19:14:07 +0000
---


A common occurance in each of my households since the release of Netflix's streaming app has been one person in the house/dorm/apartment asking "What should we watch?", and another person responding "There was this new show I wanted to watch, but I forget what it was...I guess just put on Friends again,".

Close to a decade later, I've upgraded from a paper list of shows to remember to watch to a shared Google Sheets doc between my husband and I. Far from the best solution, this option requires us to grab a phone or computer and open our doc and sort through the shows to find one that matches our mood and the amount of time we have. When it comes to watching tv after a long day, we're usually too lazy to put in this much effort.

Knowing about the upcoming Sinatra Assessment, I asked my friends to tell me what their dream app for tracking tv shows might look like. I got a lot of great suggestions for an app that:

- Shows Show Title, Episode Length, Streaming Platform it's available on, and Genre at a minimum
- Allows you to indicate which episode of the show you're currently on, and will auto-update when it detects that you've watched a new episode on one of your streaming services
- Displays whether a show is available for free on one of your current streaming services, or if it needs to be purchased, or if it is not yet released
- Alerts you when a show you watch has a new episode or season released
- Allows you to build collaborative show lists with friends/family
- Has a "Get recommendation" feature, that will allow you to plug in a few variables (such as "I want to watch a *comedy* that has at least *3* episodes of *30 min* length") and get a show recommendation based on your list

These suggestions were great and started to give me a good idea of an app I would want to start shaping, but even before I started building it, I knew I'd have to pick only a couple of these features, unless I wanted to spend a month on this project (ain't nobody got time for that, I've got Rails to learn!).

After an workshoping my idea at a study group with Luke and some of my fellow Learn-mates, my idea was whittled down to a more workable app with a Users class and a Shows class. Genre and episode length would be attributes of the Show class, but creating databases and classes for those would be getting too in the weeds for this project.

I created my app, with Users has_many Shows, and Shows belongs_to Users, and was able to fill out a pretty good working app fairly quickly. Having just completed the Fwitter project, this setup was already familiar.

Once I got that done, I decided to make an experimental branch to see just how hard it would be to add in some additional functionality for the show genres and show episode lengths.

I created migrations to add a new table for each, and made the relationships look like this:
User has_many Shows
Genre has_many Shows
Length has_many Shows
Show belongs_to User
Show belongs_to Genre
Show belongs_to Length

Since these were still simple `has_many` and `belongs_to` relationships, it didn't add too much more time and effort to include these tables. What this did was allow users to save genres and lengths, so that they 1. would only have to create a new genre/length name once, then it'd forever exist in their database and they'd be able to add new shows to that genre/length, and 2. allow for future iterations to give users the ability to sort and/or filter their shows by genre/length.

I'd already done 'checkbox' styles of adding attributes to a new object, so I thought I'd go for a more streamlined look with a dropdown list. This proved very difficult to find online resources for, since most searches returned the Rails syntax of how to build a dropdown list in Ruby. With trial and error and a lot of guessing, I ended up with:

```
<select name="show_genre">
	<option> </option>
	<% @genres.each do |genre| %>
		<% if genre.id == @show.genre_id %>
			<option value="<%= genre.id %>" selected="selected"><%= genre.name %></option>
		<% else %>
			<option value="<%= genre.id %>"><%= genre.name %></option>
		<% end %>
	<% end %>
</select>
```

where the "select" keyword indicated that the list would be a dropdown, and each value in the dropdown would be between "option" tags.

I had to play around until I figured out how to make the "/shows/edit" form automatically default to showing the current genre and length attached to the show, instead of resetting the dropdown to the first choice in the list. Using the handy `selected="selected"` tag I added some logic to indicate that if the id of the Genre object matched the id of the current page's Show object's genre id, then that genre would be selected by default in the dropdown, then the other genres would appear as other options that were visible once the dropdown was expanded.

After various other tweaks and edits, I arrived at an app that, while not the end-all solution to the problem I described in the beginning, is a working app that I'll feel happy to share with my friends and get feedback on how I can build it out to be even more useful (after my Learn.co graduation :) ).

