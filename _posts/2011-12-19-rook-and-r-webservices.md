---
layout: post
title:  "Rook and R Webservices"
tags: [R, programming]
---

Recently I have been working on setting up a webservice that does some non trivial statistical work.  Normally, my go to when building web services is Ruby/Rails due to ease of use, then I offload anything computationally intensive to something more optimized (e.g. a C or Java application on the same box).  In this case however, partly because of my co-author’s skill, partly discipline norms, and a whole lot of R being awesome for this sort of thing, the statistical work is going to be done in R.

While it would have been possible to still build the webservice wrapper in Ruby and then either use one of the existing Ruby wrappers for R (or even spinning up an R process on its own), I wanted to see if I could build the whole thing in R.  As is almost always the case, I was not the first person to think of this, and most of the hard work has already been done.

[Rook](http://cran.r-project.org/web/packages/Rook/index.html) is an R package on CRAN written by [Jeffrey Horner(http://jeffreyhorner.tumblr.com/)].  For those of you familiar with Ruby and [Rack](https://rubygems.org/gems/rack), Rook is very similar.  It provides an interface, using R’s built in rApache webserver, to handle http requests, handle routing, etc.  The more I read about it, the more I was convinced that it was a great solution to the problem that I was working on.  Now if only I get it to run on Heroku….

Well, running Rook on Heroku was surprisingly simple thanks to Noah Lorang’s example which you can find [here](https://github.com/noahhl/rookonheroku).

So, how do you get started?  Almost everything that you’ll need is in the [README](https://github.com/noahhl/rookonheroku/blob/master/README.md) in Noah’s repository, but there are a couple of tricky things to note.

First make sure that you have a Heroku account.  It is free to sign up and you get one free full-time process per project (one single web server in our case).  There are numerous resources (including their excellent help files) to get you through this part.

Next you can either walk through the instructions in Noah’s example (which I ended up doing), or you can do the much easier thing by cloning his repository.  If you do this, then you should be able to deploy it directly.

After you get a running instance of Rook, you will want to write some R code.  To run your own custom R script, replace the “/app/demo.R” in rackup file (config.ru) with the path of your script.  Otherwise, you can just put your code in demo.R.

Because Heroku is a read-only file system, you will need to include any R packages in your source tree so that they are “installed” when you deploy.  Initially you will just have R and Rook installed (if you cloned the existing project).  Because some packages require native compilation, you really should do that compilation on one of heroku’s servers.  In order to do this, you need to ssh into your app server:

```shell
heroku run bash
```

Once you are in the bash shell on your app server you can load R.  From there install any packages that will be dependencies for you project.  When you exit the R shell, do not log out of the ssh session.  If you do the app will reset and you will need to start over (remember it is read only so the file system changes persist only as long as your session).  You then need to figure out how you want to get these changes off of your heroku instance.  First zip them up (I zipped up the whole bin directoy):

```shell
tar -cvzf mybin.tar.gz ./bin
```

Then you can either scp it off of the machine (as Noah suggests):

```shell
scp mybin.tar.gz me@myserver.com:~/myproject/bin.tar.gz
```

Or, if you do not have access to a destination that you can scp (heroku does not have ftp installed), you can do the roundabout method of setting up github as a remote, checking in the tarball, pushing it to github and then cloning that repository locally.  Once you have the tarball on your machine, just untar it into your repositories bin directory, checkin, and deploy.  You will now have access to those packages in R.

I’ll writeup how to actually use Rook in my next post.