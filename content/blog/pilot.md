+++
title = "Pilot"
date = 2024-03-03
+++

Hi everyone, welcome to my website. I've been meaning to make a website for a
long time, but I've always struggled with making something that's both good
looking and functional as I'm not much of a frontend guy. Because of my general
lack of interest in the frontend world, I had no idea that static site 
generators (SSGs) existed, which make the entire process of creating a website
an order of magnitude easier.

I ended up doing some research into static site generators and found [Jamstack](jamstack.io), 
which is a great resource for this type of stuff. Looking through the options 
on Jamstack, [Zola](https://www.getzola.org) caught my eye as it's written in 
Rust, and I am nothing if not a Rust shill. I later found out that using a 
SSG is completely independent of the language it is written in, but supporting 
Rust technology is always a win for me.

Initially, Zola's project structure was super confusing to me (and it still
is to an extent). There's the basic stuff that most SSG's share, like the 
directory for content, a config file, templates, and a theme, but Zola has some 
unique parts that were increasingly confusing for me. One of them was using
markdown as a form of configuration. Most SSG's, like Hugo, for example, use 
some sort of configuration in their markdown files through the TOML `+++` 
section or the YAML `---` section, but Zola has mandatory markdown files that 
are (mostly) dedicated to just configuration through `_index.md` files. I still
don't *really* understand it, but I understand it enough to get this site 
working. I understand that it configures the section or directory, that it's
found in. For example, the `_index.md` file for the `/blog` section on this site
looks like this:
```md
+++
+++
```
Oh, weird. Well, usually, it would hold some configuration that dictates what
templates the section and pages use, what the title is, etc. I suppose mine is
empty because of the [theme I'm using](https://github.com/pants721/no-style-please). 
I found this theme on Zola's theme page, but I had a number of personal nitpicks
with it, so I just decided to create my own fork of it. As of right now, I have
a few things I need to change still:
- [ ] Make the tab title read `Section :: MySite` instead of just `Section`
- [ ] Make the dark mode not just a literal inverse of light mode

Once I settled on a theme, the development of this site was super easy. I just
had to fit it to the structure I wanted and then deploy it. As of the writing of
this post, this site is being deployed using [Github Pages](https://pages.github.com)
and the continuous integration from my repo is being done by the very cool
[Zola deploy Github action](https://github.com/shalzz/zola-deploy-action). I'm
super happy with how this has all turned out, and I think I'll be sticking with
this for a while.

There are still things I want to implement on this site that I haven't had the 
time to do today. Here's a general list that I might come back to update
- [ ] Create an about page so I can talk about myself
- [ ] Maybe add some images
- [ ] Add a footer
- [ ] Add a projects tab
- [ ] Get some more stuff on the contact tab
- [ ] Add a devices tab
- [ ] (long-term) Add CV

The intended purpose of this site is mostly to write about stuff I like, which 
includes programming, computers, basketball, volleyball, philosophy, music,
and much, much more. I have quite a lot of interests, which I'm sure I'll write 
about on my "about" page, and I think they will be a big part of this site as 
I'm still young enough to not need my website to be super professional. Another
purpose of this site is to inform prospective employers about my qualifications
and show that I am one of the few developers with the ability to read and write.

I also want to use this as a place to document my academic writing. As of right now,
I'm a junior in high school, and I'm currently procrastinating working on my
IB Philosophy IA on *The Eternal Sunshine of the Spotless Mind*. I don't know if
I'll publish that one as I'm not too optimistic about it, but I do want to publish
my IB Extended Essay comparing Camus' *The Myth of Sisyphus* and another TBA
philosophical writing about the nature of meaninglessness, and specifically how
we as humans should handle that.

That's about it for the introduction to this site; thanks for reading.
To many more!

-Lucas
