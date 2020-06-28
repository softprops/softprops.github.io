---
title: dev together
cover_image: https://images.unsplash.com/photo-1529673736833-9302d731fc8f
tags: goals,writing,reflection
published: true
---

👩🏽‍💻👩🏻‍💻👨🏽‍💻👩‍💻🧑🏿‍💻
> A group needs only two things to be a tribe: a shared interest and a way to communicate. 
> \- Seth Godin: Tribes

---

* [a goal](#a-goal)
* [from the top](#from-the-top)
* [trying size medium](#trying-size-medium)
* [rediscovering community](#rediscovering-community)
* [principle flow](#principle-flow)
* [takeaway](#takeaway)

## a goal

A goal without plan is just a wish[^1]. I've been wishing I could write more often for a very long time. It's something I know I should put more time into. I've been thinking lately about how I could reframe that wish as a goal.

Making a routine of writing is an effective means of learning how to communicate well. Communication is idea delivery. Ideas you can't transfer well to others aren't useful despite their potential usefulness. Programming is also idea delivery but programming alone without writing is like eating unbalanced diet for if you want to get better at delivering ideas.

I haven't blogged in a while so I recently starting reflecting. Written on the first page of my [junior investigator's memopad](https://en.wikipedia.org/wiki/Five_whys) are the words: 🗒️ "Question: Why don't I write more often?" 

## from the top

My blogging journey started with a humble tumblr[^2]. A few to be precise[^3] [^4]. My goal at that time was to capture what I was learning and share it with others with some hope that others might find it useful. Simply tracking links set a relatively low bar for writing but at least it created a space for sharing random things and ideas I learned on my programmer's journey. Eventually I got distracted with tumblr's theme engine and lost focus on my goal. 

I soon after felt the young developer's itch and pang of curiosity to write my own blog publishing tool. For historical context, this was during era of what I call Ruby on Rails' "golden age", back when Rails was first starting to take off. If you wanted to start a new project you were [already half way done almost as soon as you started](https://www.youtube.com/watch?v=Gzj723LkRJY&feature=youtu.be). There's still something to be said today for the virtue of being able to do that way back then, circa 2005.

As expected, my little toy posting machine ended out resembling much of what tumblr already did well and in the end, keeping the pieces of that tool in place took as much time if not more that my writing time itself. Well played past me ✋. The exercise is always more important than the artifact so I ditched the artifact and learned from the exercise. I'm glad I did. I learned the useful lesson in the cost of time spent on [undifferentiated heavy weight lifting](https://aws.amazon.com/blogs/aws/we_build_muck_s/). You write it, you run it. Whose got time to run it? Oh and it's software so you have to maintain it, so...


I stopped writing for a while and reflected for a bit after about what I wanted from a writing platform.

## trying size medium

At that time what I wanted was a quality, hands off writing platform. No maintenance. Fewer jazz hands. Just a platform to focus on writing. Soon afterwards I fell in with ["The Medium"](https://medium.com/@softprops) crowd.

The allure of medium.com when it first arrived on the scene was that it positioned itself as being [_the_ place](https://www.theatlantic.com/technology/archive/2013/08/what-is-medium/278965/) to discover quality and thought leading writing. Not to knock on a well done and nicely designed product, but what I actually experienced in practice was a platform where nearly every poster followed roughly the same cookie cutter recipe. The content eventually felt uninspired, and as such, eventually as was I. Nothing felt original.

Medium also eventually came with an exclusivity price. I found myself inflicting more self injury as time passed  stubbing my feet [tripping over paywalls](https://help.medium.com/hc/en-us/articles/360017581433-About-the-metered-paywall) while trying to consume content I got push notifications about than I did absorbing new ideas. As a content creator I felt odds with the product asking me to pay for a service I was actively contributing content to.

It's a solid product but ended out not feeling like something I felt invested in. Something was missing.

So I dialed writing down to 0 for a while again.

# rediscovering community

These days I mostly offer small denominations of [code graphics](https://carbon.now.sh/) embedded in tweets but I've found even that quickly loses its novelty. It's also not horribly accessible! What Twitter lacks is ample space to communicate context, [the why](https://www.ted.com/talks/simon_sinek_how_great_leaders_inspire_action), and most importantly -- what I learned. What I wanted was a place to share both long and short TIL posts.

I've recently revisited the idea of hand crafting a blog but rather than writing a tool from scratch for the joy of reinventing existing publishing mechanics, I wanted a place to explore creativity using the the often forgotten raw materials of the web: HTML, CSS and JavaScript. The HCJ stack doesn't have a cool ring to it but It will make a come back. Just you wait! I don't get to exercise those muscles enough and I feel my inner designer belly needs some situps. I didn't want to fall into a trap of maintaining a new tool. Fortunately, [I don't have to and neither do you](https://www.staticgen.com/)!

Around that same time I rediscovered [dev.to](https://dev.to/). I'd actually already heard about dev.to much earlier but ignored it without good excuse. I now regret that. It was only by chance that I saw [this tweet](https://twitter.com/leewynne/status/1268567098101547009?s=19) which caught my eye with this graphic.

![dev.to image](https://pbs.twimg.com/media/EZrbAfbWAAIlhnf?format=jpg&name=900x900)

This tweet reminded me of another reason I sometimes avoid writing: the internet troll patrol and the reserve natural guard of developers just waiting to be deployed when [someone is wrong on the internet](https://xkcd.com/386/). I think this is currently the biggest draw of dev.to and the thing I've been missing with writing platforms: a supportive community.

What I really dig about dev.to is that the more time you spend with it, the more you'll start to notice its primarily an vibrant and supportive developer community and only secondarily a blogging platform. Posting articles is just a mechanic to that end. [Community is the real product](https://dev.to/about) being offered. For those that know me, that's a my peanut butter and jam. The reason why I'm packing up and moving to dev.to is the community.

# principle flow

After Medium, I have stronger, renewed principles on what a writing platforms ought to be. If monetizing still is your thing, [dev.to has a nicer more open approach](https://dev.to/devteam/dev-is-now-web-monetized-21db).

But there's one more thing. My writing so far hasn't really be set up to adapt to change. My writing lives inside the platforms that I've used in the past. It seems very probably that I'll continue to move around so it makes sense that I can carry my content with me where ever I go next. I'd like to be able to [live more portably](https://open.buffer.com/living-one-bag/) so that I am better equipped for future change. 

Offline authoring is a direction I've also wanted. I am I fan of feeling the [flow](https://en.wikipedia.org/wiki/Flow_(psychology)) when trying to think creatively. Offline writing avoids online distractions and gives you space to think.

Here's the flow I'm currently iterating towards.
 
* Write everything locally, offline
* Store posts in a GitHub code repository
* On git pushes, trigger a GitHub action that
   * builds a static content site and publish that to gh pages
   * run a tool to upload the same content to dev.to where I can make new friends and not have think about content discovery.
   
In a separate post, I'll write in more detail this setup in more detail and [a little tool I wrote](https://github.com/softprops/devtogo) if you are interested in using the flow yourself.
 
To me, that's best of all the worlds: distraction free writing, liberated content, participating more in an open and friendly community, and creating space to explore the HCJ stack and work on my web love handles.

## takeaway

It's useful reflect often.

Reflecting will give you a better sense for your principles. You'll also learn how to be happier and find what you are looking for.

What I learned is that my goal wasn't just creating routine for writing. It was finding a community to share it with.

[^1]: https://www.goodreads.com/quotes/87476-a-goal-without-a-plan-is-just-a-wish
[^2]: https://softpress.tumblr.com/
[^3]: https://asoftsea.tumblr.com/
[^4]: https://papertail.tumblr.com/