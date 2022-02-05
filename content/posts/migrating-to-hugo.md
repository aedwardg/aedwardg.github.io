---
title: Migrating to Hugo
date: 2022-02-04T11:10:19-08:00
categories: [ Blogs, Meta ]
tags: [ Hugo, Congo, Chroma, Syntax highlighting, blogs, meta ]
---

Over the years I've made several attempts to keep a blog, but they've all more
or less fallen somewhere on the spectrum of failure. Perhaps I should lower my
standards and call a blog a success if it reaches more than two posts.  I think
at that point I may be the proud author of _two_ successful blogs (give or take
one...it's been a while).

This is all to say that blogging is hard to keep up with&mdash;particularly if
you're working with tooling that isn't intuitive and painless. You already have
the hurdle of putting your thoughts into coherent sentences working against you,
why make it harder on yourself by using insufficient tools for your needs? Hence
my switch to [Hugo][1] for this second attempt at a developer blog. I'm mildly
optimistic that I'll be able to keep it up for longer this time. :smirk:

## Why Hugo?

So why, after so many colossal flops, am I switching to Hugo? It's simple, really.
The more times I make a failed attempt at blogging, the more I realize what I do
and _don't_ want in my blogging toolset.

This is particularly true after my
[most recent blogging endeavor](https://blog.anthonysdata.com/old-blog), and my
first ever development blog. I had about a year of Python under my belt and fell
for the myth that you need to build a hand-made portfolio website and also blog
about your studies and projects in order to make a career change into the tech
industry as a self-taught programmer.

So I did what any good aspiring data scientist does: built a mediocre HTML/CSS
site from scratch, looked at Medium, got overwhelmed by the swarms of other data
noobs pumping out articles for [_Towards Data Science_][2], and promptly
Googled&mdash;in full naïveté&mdash;"static site generator python". From there,
I found [Pelican][3], found an extension that allowed me to write my blogs in
Jupyter notebooks and went to town.

I wouldn't say my experience with Pelican was _bad_, but it wasn't great either.
I didn't know a whole lot about development, deployment or GitHub when I set it
up. GitHub Actions for a free build/deploy pipeline wasn't really a thing yet.
And, despite my best customization attempts, I just couldn't find any themes that
were nice, modern and had a dark mode. Add on the pressure of writing content with
the aim of finding a job, and it just wasn't an enjoyable experience overall. I
made some [cool visualizations](https://blog.anthonysdata.com/old-blog/nyc-sat-2.html)
that nobody will probably ever see, and wrote some
[mildly informational articles](https://blog.anthonysdata.com/old-blog/sqlite-at-home-2.html)
to help others at my skill level with data science tools, but in the end it was
simply too much work.

Since my last post in July 2020, I've been looking for a better way to blog. I
researched and tried a number of different options, but ultimately Hugo won me
over. Below is a list of my blogging criteria in case you find yourself in a
similar position and are curious why I chose Hugo.

## Things I Want When I Blog

### 1. Modern Theme

One of the big downsides of using Pelican was the lack of modern themes available.
My last blog didn't look _terrible_, but it also didn't look modern. It looked
fine. Pelican isn't the only static site generator to suffer from this either.
[Jekyll][4], probably the biggest name in static site generators, also has a lot
of "blah" themes. It has plenty of nice ones too, but the [nicest ones](https://jekyllthemes.io/)
usually cost, which is a deal breaker for me. Hugo, on the other hand, has enough
modern and **free** themes that I had no trouble finding a suitable one with a
quick search.

### 2. Easily Customizable Theme

While some options have modern themes out of the box (like Medium), they're
either not customizable at all, or they're a pain to customize. Jekyll and Pelican,
for example, might have a way to customize your site to your liking, but there's
a good chance you have to have to look for extensions that achieve what you want.
Between Hugo and the [Congo theme][5], I have more flexibility and customizability
than I'll probably ever need baked right in.

### 3. Syntax Highlighting

Perhaps surprisingly in 2022, built-in syntax highlighting with decent colors is
harder to come by than you'd think. Some options have it built in, but you get
terrible colors out of the box. Others require you to add an extension yourself.
And worst of all, some (I'm looking at you, Medium :unamused:) have historically
not supported syntax highlighting in code blocks and required you to link to a
GitHub Gist instead.

Hugo comes with [Chroma][6] built in and I find the themed highlighting from
Congo pleasant enough with a little tweaking and I trust my readers will as well.

```ruby
def say_hello(name)
  puts "Hello, #{name}!"
end
```

### 4. Ease of Use

One of the most important aspects for me is how easy it is to get from zero to
published article. Some might argue that Hugo has a steeper learning curve than
other SSGs, and that using a WYSIWYG editor like the one Medium provides is
technically easier than having to style your work with Markdown and organize your
own directory structure yourself.

I won't disagree with them.

I will, however, say that in the long run (after the initial setup pains) it's
easier for me to get content out the door using the Markdown/Hugo/GH Pages/Actions
flow publishing flow. I've been writing in Markdown for years now, whether it's
for code documentation or chatting on Discord and Slack.  In fact, I've written
far more in Markdown in the last few years than I have in a regular word processor.
I also use Git and VS Code every day, so there's no change of tooling required.
Simply make a new file, write my thoughts, `git add`, `git commit`, `git push`.

That's about as simple as it gets.

### 5. Dark Mode for Reading _and_ Writing

This one might seem like a simple ask, but I spend all day writing my code in a
dark-themed editor and I simply can't be expected to write a blog post on a
glaringly white screen. Likewise, I anticipate most of my audience will be
developers or people who write some code on a semi-regular basis and would
appreciate a dark mode option when consuming my content.

Blogging with Hugo allows me to write in VS Code with whatever color scheme I
want, and publish to a site that allows easy switching between dark and light
modes for my readers. What more can I ask for?

## Things I _Don't_ Want When I Blog

### 1. Design My UI From Scratch

Despite the persistent lie that you are somehow a lesser developer if you don't
code your personal website/blog/portfolio from scratch, I have absolutely no
desire to do this. The return on investment is low at best, and the cost is much
higher than any other option. 

I am not a designer, and I'm perfectly comfortable
with that fact. This blog is meant to be a vehicle for my thoughts and a reference
for others (and my future self), so I'll stick to what I'm good at and leave the
beautifying to people who love that sort of thing. Thanks to the hard work of
[@jpanther][5], all I had to do to make this blog look beautiful was run the
`hugo mod init` command and tweak a little bit of code git get it to my liking.

### 2. Diminished Content Control

When I'm blogging, I want complete control of how and when I publish my work. I
also don't want to relinquish any rights to the content. 

Although the idea of
submitting articles to some of the bigger programming publications on Medium, _et
al._, to get readers is tempting, I can't get on board with the fact that there
are limitations on _what_ I can write and _how_ I'm allowed to write it. Sorry,
not sorry&mdash;I'll stick to self-publishing.

### 3. Drag and Drop (or WYSIWYG)

I'll admit there's a certain appeal to blogging platforms with WYSIWYG editors
and draggable images. I even strongly considered going with [Ghost][7], which
has both of those things and more. But ultimately, all of those things are
non-essentials and usually come hand-in-hand with other restrictions that I'm
not willing to accept. So for me, WYSIWYG isn't a deal breaker (I might even
consider Ghost again if I had no life and felt like hosting and maintaining my
own instance of it), but it's also not a selling point either.

### 4. Change Software or Platform

I alluded to this [above](#4-ease-of-use), but having to change software and/or
platform in order to write blog posts comes at a cost.  It's one extra thing that
I have to learn.  One extra application that I have to open up on my computer.
Sometimes more. If I can avoid this and write using the software I already use
on a daily basis, I will. Hugo allows me to do this and that's enough for me.

### 5. Being Forced to Use Gists

Going hand and hand with [syntax highlighting](#3-syntax-highlighting), a huge
pet peeve of mine is not being able to include code directly in the blog post,
instead requiring authors to put their code in a Gist and embed the Gist in the
article. Last time I considered Medium, they were forcing writers to use Gists
like this and it was probably the biggest turnoff of the platform for me.

Although it looks like this
[may no longer be the case](https://medium.com/pythoneers/9-different-ways-to-embedded-code-in-medium-9213cb4c0a2e),
it's still something I look out for when comparing blogging options.

## Conclusion

After months of considering the various options, I settled on Hugo. It works
great for my needs, and it doesn't have any of the negatives that I was trying to
avoid. Whether it will help me get articles out to the world is anyone's guess,
but I'd be willing to bet that the reduction in technical and cognitive resistance
will count for something.

[1]: https://gohugo.io/
[2]: https://towardsdatascience.com/
[3]: https://getpelican.com/
[4]: https://jekyllrb.com/
[5]: https://github.com/jpanther/congo
[6]: https://github.com/alecthomas/chroma
[7]: https://ghost.org/