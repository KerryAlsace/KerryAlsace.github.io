---
layout: post
title:  "AngularJS Searching and Filtering"
date:   2017-01-16 01:05:14 +0000
---


For my final project for Flatiron School's online Web Development program, I created a [Video Game Wiki](https://github.com/KerryAlsace/video-game-wiki) app built using AngularJS and Rails. One of the feature requirements for this project was to add search and filter capabilities.

Angular makes searching incredibly easy. I wanted to add a search feature to the index page for Games. On that page, I was making use of the `ng-repeat` feature of Angular, which allows you to add a single line that will be dynamically repeated for however many items are in the array of items that you wish to repeat. For example:

```
<ul>
    <li ng-repeat="game in vm.games">{{ game.title }}</li>
</ul>
```

What this line of code does is take an array that I had defined in my controller, `vm.games`, and goes through each item in that array and makes a line item for it. If `vm.games` were equal to `[{title: "Overwatch"}, {title: "Dragon Age"}, {title: "Uncharted"}]`, then in execution, the above line of code would expand and become:

```
<ul>
    <li>Overwatch</li>
		<li>Dragon Age</li>
		<li>Uncharted</li>
</ul>
```

The `ng-repeat` directive already makes rendering a list of items incredibly easy. If you want to add a search capability on top of that, it's as easy as adding another directive to your `<li>`, and a input for entering your search term:

```
<input type="text" ng-model="search"></input>

<ul>
    <li ng-repeat="game in vm.games | filter: search">{{ game.title }}</li>
</ul>
```

When there's nothing entered into the search input, all the games in `vm.games` will be displayed. Once you or a user starts typing into the search input, the list of games will filter itself based on what was entered into the search. And that's all you have to do to enable search!

In addition to search, I wanted to add a way to click and filter the games list by a predetermined attribute, such as video game platforms. The idea was to be able to click to see all games that were available on PS4, for example.

To do this, I decided to use the `$filter` provider. To use this, you first inject it into the controller in which you'll use it:

```
function PlatformController(platform, GamesFactory, $filter) {
    // controller code here
}
```

As you can see here, I've also created a GamesFactory for making http calls and communicating with the Rails backend. In combination with `$filter`, I can call the GamesFactory from my PlatformController to grab my list of games, then filter them before displaying them on my Platform Page. To do this, I first make the call to GamesFactory to get the games:

```
function getGames() {
    return GamesFactory.getGames()
                                  .then(setGames)
}
```

Then within the `setGames` function, I can use filter:

```
function setGames(data) {
    return vm.games = data.filter(function(game) {
        return game.platform_ids.includes(vm.platform.id)
    });
}
```

Now I can use `vm.games` with `ng-repeat` like I did on my Games index page, but they'll be pre-filtered to only show the games that are available on the Platform whose page I'm on.
