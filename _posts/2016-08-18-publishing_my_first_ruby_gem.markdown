---
layout: post
title:  "Publishing My First Ruby Gem"
date:   2016-08-18 16:17:11 -0400
---


One of the final projects for the Object Oriented Ruby lessons at Learn.co is to create your own Ruby Gem that uses a CLI.

We'd learned a lot up to the point where I encountered the project, but it still felt like a big leap to go from forking lessons and completing labs to building an entire gem from scratch. Luckily, the internet is full of great resources if you don't mind digging around to find answers to your questions.

Before starting my project, I watched a great [video](https://youtu.be/_lDExWIhYKI) by Avi Flombaum, which I coded along with and made a copy of the daily-deal gem that Avi creates in the video. This video was immensely helpful and demystified the process of creating a gem. Once all the structure was in place, you code it like any of the other ruby programs we've made so far!

From here, I immediately had the idea to create something that could pull in info from Meetup.com. Since beginning my code journey, I've been attending WomenWhoCode Meetups and have found the group to be tremendously welcoming and encouraging, as well as being an excellent resource for lessons in various development topics. I check their [Meetup](https://www.meetup.com/WomenWhoCodeNYC/) page often to see if new events have been added, or to double check the dates and locations of events I attend. Wouldn't it be great if I could quickly check that without even having to leave Terminal?

To get started, I checked out [Bundler.io's](http://bundler.io/v1.12/guides/creating_gem.html) page to find out what commands to use to create a gem. After installing bundler and running 'bundle gem meetups-wwcnyc' *(side note: I actually had a different gem name initially. As you'll see in my commits, I edited the name of the gem a couple times until I was satisfied that the gem name was both indicative of it's purpose and memorable.)* I added the spec.md file from the project requirements and pushed everything up to the the new github repository I created.

The actual gem coding went fairly easily. When I got errors, I understood them and was able to fix them (or at least change them and get a different error). I eventually had a working gem!

Here's where things started to fall apart.

I now had a working gem, and even had it on github for the world to see and use. Time to fire up terminal and type `$ meetups-wwcnyc` to see it in action!

> ```
> ERROR while executing gem ... (Gem:CommandLineError)
> 
> unknown command meetups-wwcnyc
```

Huh, why doesn't it work? I ran `ruby bin/meetups-wwcnyc` to make sure my program still worked. Everything worked fine. Maybe I wasn't done? Is there another part to making a gem to enable calling it from the command line?

There is! I hadn't known how to actually publish a gem to [RubyGems](https://rubygems.org/). "No problem, there's definitely clear documentation for that," I thought. 

Documentation there was, but clear (to a just-built-my-first-gem user like me at least) it was not. The RubyGems [Publishing](http://guides.rubygems.org/publishing/) page instructed me to `$ gem push my-gem-name-0.1.0.gem` then "Congratulations! Your new gem is now ready for any ruby user in the world to install!". 

Easy enough. I ran `$ gem push meetups-wwcnyc-0.1.0.gem` in terminal and saw...an error.

At this point I was not forward-thinking and did not stop and screenshot the error for future blogging, so I do not remember which error I got, but if I were to guess I imagine that the error occured because at this point, the "meetups-wwcnyc-0.1.0.gem" file didn't exist. A .gem file doesn't get created when running 'bundle gem', and I hadn't seen anything so far that indicated that a gem needed a .gem file in order to be published.

Without hindsight, though, I first thought there might be something wrong in my meetups-wwcnyc.gemspec file. I looked through and saw 

```
  # Prevent pushing this gem to RubyGems.org. To allow pushes either set the 'allowed_push_host'
  # to allow pushing to a single host or delete this section to allow pushing to any host.
  if spec.respond_to?(:metadata)
    spec.metadata['allowed_push_host'] = "TODO: Set to 'http://mygemserver.com'"
  else
    raise "RubyGems 2.0 or newer is required to protect against public gem pushes."
  end
```

Ah, I bet if I comment that part out everything will work.

...

Nope! Still an error.

Cue more googling. I found a plethora of information about "How to create a ruby gem" and even "How to create a ruby CLI gem" which basically run through all of the steps I had already done, but which stop short of detailing how to publish a gem to RubyGems. 

After a reading through numerous blog posts, stack overflow discussions, and documentations, and trying various other commands and getting various other errors, I took a break. Never underestimate the power of temporarily removing yourself from an issue and coming back to it with fresh eyes.

A sandwich and some time playing with my dog later, I came back to my computer. I don't remember which order I did all of these things, but I know that, little by little, I made a change that worked and got me closer to finding another resource to point me in the direction of the next little change to make. Here are some of the key things I had to do in order to get my gem published:

From [DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-package-and-distribute-ruby-applications-as-a-gem-using-rubygems):

> Alternatively, you can benefit from Bundler's Rake tasks. You can see a full list with the following:
> 
> `rake -T`
> `rake build`:
> 
> Build my_gem-0.0.1.gem into the pkg directory
> 
> `rake install`:
> 
> Build and install my_gem-0.0.1.gem into system gems
> 
> `rake release`:
> 
> Create tag v0.0.1 and build and push my_gem-0.0.1.gem to Rubygems
> 
> If you call rake release, your package will be pushed to your set Git account and then to RubyGems.org

Before I had been missing these key commands. `rake build` and `rake install` worked! `rake release` gave me an error, but at least this time it was a new error, and I felt closer to a solution.

From [this blog](http://robdodson.me/how-to-write-a-command-line-ruby-gem/): 

> change the `gem.executables` line from
> 
> `gem.files.grep(%r{^bin/}).map{ |f| File.basename(f) }`  
> to
> 
> `gem.executables   = ["zerp"]`  

More googling led me to find this ("zerp" being a placeholder for the name of the gem.) I don't know exactly why that worked, and neither does the blog publisher, but with this change, I was able to successfully run `rake build`, `rake install`, and `rake release`!

I went to the website that the output from running `rake release` directed me to, and saw my shiny new [gem](https://rubygems.org/gems/meetups-wwcnyc/).

I tried it out by uninstalling the gem from my computer (`gem uninstall meetups-wwcnyc`) then reinstalling it from the instructions on its RubyGems page, then came the moment of truth:

`$ meetups-wwcnyc`

and it worked!

*(Side note: it actually didn't. I forget the order of how things happened, but I know that I was able to get a version of my gem published on RubyGems, but when I tried to run it from terminal for the first time, it didn't work. I did more googling and found whatever of the above solutions fixed that, then I had to figure out (more errors and googling) how to republish and/or update a gem, then finally I got version 0.1.1 pushed to RubyGems and had the moment described above).*

I took a good long moment to bask in that feeling. Then I decided I could make the program's output look better (now that I knew it was out in the world for anyone to see), so I did some refractoring until I was happy (enough) with it.

Knowing that this is only the first big Learn.co project, and that I'll have an assessment then likely do refractoring after that, I've decided to leave my gem be for now. Though the process has inspired me and given me so many ideas for improving my gem, I've got so much to learn, so I'm going to get back to that!
