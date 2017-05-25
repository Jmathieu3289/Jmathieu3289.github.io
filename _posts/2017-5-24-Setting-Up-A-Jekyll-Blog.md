---
layout: post
title: Setting up a Jekyll blog
---

It seems fitting that my first post is about actually getting this blog up and running...

[Github Pages](https://pages.github.com/) is a nice service for setting up a simple static site, and it seemed fitting to host a blog here and have it tied directly into my Github account. Simplicity is key, and having another account to manage for some other blog site or a personal website seemed a bit overkill. 

The setup is relatively straight-forward as explained in the link above, but generally the process is to create a repository with the name [your username].github.io. One important note, *the name is case sensitive and will completely break if you capitalize something you shouldn't.* I learned this the hard way and spent 15 minutes staring at a 404 page wondering why a simple index.html file in the root directory was not showing up.

Once set up, I could serve up a basic webpage. This is pretty boring though, and since I wanted to do something a bit more elaborate, I started reading into the [documentation](https://help.github.com/categories/customizing-github-pages/) about using [Jekyll](https://jekyllrb.com/) as a static site generator. 

The process for getting a project up and running seemed easy enough, but before I could even get started there was an issue. Jekyll isn't supported on Windows, [mostly](https://jekyllrb.com/docs/windows/). Since the gist of the guide involved getting [Bash on Ubuntu on Windows](https://msdn.microsoft.com/en-us/commandline/wsl/about?f=255&MSPPError=-2147217396) (because sure, why not), I decided to skip the middle man and spin up a Docker container using a basic ubuntu 16.04 image.

The Docker setup was pretty easy, using the official [Ubuntu](https://hub.docker.com/_/ubuntu/) image, there wasn't too much work to do other than mounting a volume for my project and forwarding port 4000, the default port jekyll uses for its built in development server.

{% highlight %}
docker run -p 8080:4000 -v "C:\Users\Justin\Documents\Github\Jmathieu3289.github.io":/var/jekyll --name jekyll ubuntu:16.04
{% endhighlight %}

This starts a Docker container with the name jekyll using ubuntu:16.04 as the base image. To explain the other flags...

{% highlight %}
-p 8080:4000 
{% endhighlight %}

...to publish the server's port 4000 to my local port 8080, so that I can access the Jekyll site from a web browser, and...

{% highlight %}
-v "C:\Users\Justin\Documents\Github\Jmathieu3289.github.io":/var/jekyll
{% endhighlight %}

...to mount a local folder to (in this case my cloned github repo) to a folder inside the Docker container. This way when doing builds within the container, the changes can be commited to my github repo.

