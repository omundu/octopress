---
layout: post
title: "Setting up Heroku deploys on Octopress"
date: 2014-03-26 13:23:24 -0400
comments: true
categories: [how-to, octopress, hacks]
---

Octopress does a great job in setting you up to use heroku to host your site. Details for the almost trivial set up can be found [here](http://octopress.org/docs/deploying/heroku/). To summarize, after installing and setting up Heroku, create a Heroku repository as per usual:

```
heroku create
```

Edit the `.gitignore` file and remove `public` so that the it is included with git changes and then run the following:

```
rake generate
git add .
git commit -m 'site updated'
git push heroku master
```

You will need to run the above every time you add a new post so to make things easier, you can add a rake task to the Rakefile to handle the less exciting bits for you. Mine looks like:

``` ruby rake task to deploy to heroku
desc "Deploy website via Heroku"
task :heroku, :commit_message do |t, args|
  if args.title
    message = args.commit_message
  else
    message = get_stdin("What updates are you pushing? ")
  end

  Rake::Task[:generate].execute

  puts "## Deploying website via Heroku"
  system "git add ."
  system "git commit -m \"#{message}\""

  puts "## pushing updtates: head ---> origin"
  system "git push origin head"

  puts "## pushing updtates: master ---> heroku"
  system "git push heroku master"
end
```

And with that, all I have to do new after editing or creating a post is simply run:

``` plain
rake heroku
```

And life is good.
