---
layout: post
title: Getting into Git
date: 2011-10-4 13:10 -0400
tags: git talks
---

This monday, I was asked by [Mike Ward(https://web.duke.edu/methods/)] to give a talk to his ICEWS group at Duke about the wonders of source control and how to get started using [git](http://git-scm.com/).  I was excited to do it because I am a big fan of git and use it on almost everything that I create myself (yes I use it on my CV…).  While the opportunity to introduce it to others was great, I went into it a little bit of trepidation because in the past when I have talked about git or other DVC systems, my audience is at least sold on the idea of version control… because they are software developers.  That was not the case for this audience, but I think I may have converted them nonetheless.

Given the audience, I chose to skip over most of the technical reasons why git is nice.  If you don’t have experience with other forms of source control then it is tough to get sold on the particulars of git (distributed, easy branching, etc.).  This group did however have a code base that multiple people were working on and currently sharing through [dropbox](https://www.dropbox.com/). So, I wanted to at least sell them on how Version Control and a distributed host (like [github](https://www.github.com/)) would make their lives better.

We started by walking through the steps of:

- Initiating a repository

- Committing a change

- Pushing it to a remote host

- Cloning that repository

- Pulling changes

At this point one of the audience members asked the obvious question about “what happens when people commit conflicting things?”  This had been a major problem when there was only one instance of the codebase and Dropbox was syncing it.  This was a great question because it segued perfectly into resolving merge conflicts.  We did one of these by hand to show how git handles that and then spent some time looking at github and its nice presentation of commits, branches, etc.  This really seemed to resonate with the audience.

We did not get to branching during the talk, so I wanted to follow up here for those that were interested.  Branching is a way of making a parallel version of the codebase.  If for example, you wish to make some major changes to a library but need to keep the main branch of the build working because others will be editing it as well, you create a branch.  This parallel version of the codebase can then be edited by you.  You can commit changes and even push them to remotes because this is all happening in parallel to the master branch.  When you are done with your work you can merge your changes back into your master branch.  This takes the commits that you made on your branch and applies them to master (very much the same process as a git pull command).  This probably best understood visually so checkout the great explanation on <https://learn.github.com> for more.

Any feedback on how to make this presentation more relevant to Political Science would be appreciated.  My goal was to really focus on how it could help in the immediate term and not get bogged down in the technical reasons for why git is good (of which there ware many).  I was happy to see that by the end of the talk a few of the audience members had already setup repositories for both their R codebases as well as their papers that they were doing in LaTex.  Here is my slidedeck, nothing groundbreaking here, but a reference nonetheless: <https://www.slideshare.net/josh_cutler/intro-to-git-9637383>