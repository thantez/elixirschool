---
author: Bobby Grayson
categories: general
date: 2019-02-03
layout: post
title: Getting a Plug App Working With Heroku
---

In our [previous_post](TODO_LINK) we built a very simple Portfolio app with `Plut.Router`.
In this (brief) post, we will show how to get things up and running on [Heroku](http://heroku.com) for a vanilla `Plug` app.
It really is quite easy, but today when I set out to do it, the resources I found either focused on Phoenix or had a few holes, so I figured I would write up a simple one here.

## Let's Do It!
To deploy to Heroku, your first step is always going to be your `Procfile`.
The `Procfile` essentially determines what boots up your app.
Ours is very simple, we will put it at the top level.

```
web: mix run --no-halt
```

Thats it!

Now, the reason Heroku is so easy is because they have a _lot_ of built in tooling to make deploying your app easy.
In the case of Elixir, we need a buildpack that will handle our compilation and to run the app and get the proper dependencies on our server.
To get any further, you will need a Heroku account and to have their toolbelt installed.
See [their instructions](https://devcenter.heroku.com/articles/heroku-cli#download-and-install) to get up and running.

Once you have this, run `heroku login`, and then `heroku create`.
This gets the basics set up for us.
Now, we can add our buildpack.

```
heroku buildpacks:set https://github.com/HashNuke/heroku-buildpack-elixir
```

Now, we need to set our env to prod on heroku:

```
heroku config:set MIX_ENV=prod
```

We also need to give our buildpack some config so it knows what our Elixir/Erlang environments are like.

```
#./elixir_buildpack.config
erlang_version=21.1
elixir_version=1.8
```

Now, we want to set a few configs for heroku:

```
$ heroku config:set MIX_ENV=prod
$ heroku config:set PORT=4001
```

Now that we've set this, let's change our application initialization to use the configured port from the env:

```
# lib/router_example/application.ex
  
  # ....

  def start(_type, _args) do
    children = [
      Plug.Cowboy.child_spec(scheme: :http, plug: RouterExample.Router, options: [port: application_port()])
    ]

    opts = [strategy: :one_for_one, name: RouterExample.Supervisor]
    Supervisor.start_link(children, opts)
  end

  defp application_port do
    System.get_env()
    |> Map.get("PORT", "4001")
    |> String.to_integer()
  end

  # ....
```

With this, we add a function to get our port.
We either get it from the environment, or default to port 4001.

Now we can get it up in the real world:

```
$ git push heroku master && heroku open
```

And after that push succeeds, we can see our portfolio site live and working in the real world.

We don't go over the granularity of test/dev/prod configuration here.
Check out the [mix config docs](https://hexdocs.pm/mix/Mix.Config.html) for more on that.
We also haven't wired up a database or anything along those lines, but that is also a topic for another day or blog post.

Happy Hacking!
