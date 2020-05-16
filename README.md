<div align="center">

# Phoenix Content Negotiation _Tutorial_

A tutorial showing how to return _different content_ (format)
for the _same route_ based on `Accepts` header.

</div>

## Why? 🤷

This tutorial shows how simple it is
to turn _any_ Phoenix Web App into a REST API
using the _same_ routes as your Web UI.




### Goal? 🎯

Our goal is:
to run the _same_ Phoenix Application for both our Web UI and REST API
and have the _same_ route handler (Controller)
_transparently_ return the appropriate content (HTML or JSON)
based on the `Accept` header.

So a request made in a **Web Browser** will display HTML
whereas a cURL command in a terminal
(_or request from any other Frontend-only App_)
will return JSON for the _same_ URL.

That way we ensure that _all_ routes in our App
have the equivalent JSON response
so _every_ action can be performed _programatically_.
Which means _anyone_ can build their _own_ Frontend UI/UX
for the @dwyl App.
We believe this is _crucial_ to the success of our product.
We think the API _is_ our Product and the Web UI
is just _one_ representation of what is _possible_.

<br />

## What? 💡

In our
[App](https://github.com/dwyl/app)
we want to ensure that
_all_ requests that can be made in the `Web UI`
have a corresponding `JSON` response
without any _duplication_ of _effort_.
We _definitely_ don't want to have to
run/maintain two _separate_ Phoenix Apps
as we know (_from experience_)
that the functionality will diverge
almost immediately
as a contributor who is building their own UI
will make an API-focussed addition and _forget_
to add the corresponding web UI (_or vice versa_).
We don't want to have to "_police_" the PRs
or _force_ anyone to have to write the same code twice.
We want a JSON response to be _automatically_ available
for every route and never have to think about it.
We want _anyone_ to be able to build an App/UI
using our API.



### What _Is_ Content Negotiation? 💭

> _**Content negotiation** is the process of selecting
the best representation for a given response
when there are multiple representations available._"
~ [RFC 2616/7231](https://tools.ietf.org/html/rfc7231#section-3.4)

The gist is that depending on the `Accept` header
specified by the requesting agent (e.g. a Web Browser or script),
a different _representation_ of the content can be returned.


If the concept of HTTP content negotiation is new to you,
we suggest you read the detailed article on MDN (5 mins):
https://developer.mozilla.org/en-US/docs/Web/HTTP/Content_negotiation


### What Are We Building? ✨

The aim of this tutorial is to demonstrate
content negotiation in a real-world scenario. <br />
We are going to build a simple interface to display
famous quotations, both a basic Web UI and REST API. <br />
When people visit: `/quotes/random` they will see a random quotation. <br />
When they visit: `/quotes/:id` (e.g: `/quotes/42`)
the 42<sup>nd</sup> quote in the
[`quotes.json`](https://github.com/dwyl/quotes/blob/master/quotes.json)
list.



### _Try_ It! 💻

_Before_ you attempt to follow the example,
Try the Heroku example version so you know what to expect.


#### Browser 📱

Visit:



Random

By ID:





#### Terminal ⬛

Run the following command:


<br />

## Who? 👤


This example aimed at _anyone_ building a Phoenix App
who wants to _automatically_ have a REST API. <br />
For us [`@dwyl`](https://github.com/dwyl/app/issues/273)
who are building our API and App Web UI simultaneously,
it serves as a gentle intro to the topic.

If you get stuck or have _any_ questions,
please
[_ask_](https://github.com/dwyl/phoenix-content-negotiation-example/issues).

<br />

## How? 💻

### Prerequisites? ✅

This example assumes you have `Elixir` and `Phoenix`
installed on your computer
and that you have some _basic_ familiarity
with the language and framework respectively.
If you are totally new to either of these,
we recommend you _first_ read:
[github.com/dwyl/**learn-elixir**](https://github.com/dwyl/learn-elixir)
and
[github.com/dwyl/**learn-phoenix-framework**](https://github.com/dwyl/learn-phoenix-framework)

Ideally follow the "Chat" example
for more detailed step-by-step introduction to Phoenix:
[github.com/dwyl/**phoenix-chat-example**](https://github.com/dwyl/phoenix-chat-example)

Once you are comfortable with Phoenix, proceed with this example!

<br />

### 0. Run the _Finished_ App ⬇️

We encourage everyone to
["_Begin With the End in Mind_"](https://en.wikipedia.org/wiki/The_7_Habits_of_Highly_Effective_People#2_-_Begin_with_the_end_in_mind)
so suggest that you run finished App on your `localhost`
_before_ attempting to build it.
Seeing the App _working_ on your machine will
give you confidence that we will achieve our objectives (defined above)
and it's a good reference if you get stuck.

If for any reason it _doesn't_ work, you can
[**open an issue**](https://github.com/dwyl/phoenix-content-negotiation-tutorial/issues)

#### Clone the Repository 📋

In your terminal, clone the repo from GitHub:

```
git clone git@github.com:dwyl/phoenix-content-negotiation-tutorial.git
```

#### Install The Dependencies 📦

Change into the newly created directory and run the `mix` command:

```
cd phoenix-content-negotiation-tutorial
mix deps.get
```


#### Run the App 🚀

Run the Phoenix app with the following command:

```
mix phx.server
```

You should see output similar to the following in your terminal:

```sh
[info] Running AppWeb.Endpoint with cowboy 2.7.0 at 0.0.0.0:4000 (http)
[info] Access AppWeb.Endpoint at http://localhost:4000
```


#### Test it in your Browser 🖥️

# TODO: update with screenshot of _finished_ app




#### Test it in your Terminal ⬛

# Todo: update with screenshot of terminal response





Now that you know the end state of the tutorial _works_,
change out of the directory (`cd ..`)
and let's re-create it from scratch!

<br />

### 1. Create New Phoenix App

In your terminal, run the following command to create a new app:


```
mix phx.new app --no-ecto --no-webpack
```

When asked if you want to `Fetch and install dependencies? [Yn]`
Type <kbd>Y</kbd> followed by the <kbd>Enter</kbd> key.

> **Note**: This example only needs the bare minimum Phoenix;
we don't need any JavaScript or Database. <br />
For more info, see:
https://hexdocs.pm/phoenix/Mix.Tasks.Phx.New.html <br />
> The beauty is that this _simple_ use-case
is identical to the _advanced_ one.
Once you understand these basic principals,
you "grock" how to use Content Negotiation
in a more advanced app.

> **Note 2**: We default to calling _all_ our apps "App" for simplicity.
Some people prefer other more elaborate names. We like this one.

> **Note 3**: We have deliberately made this API "read only",
again for simplicity.
If you want to _extend_ this tutorial
to allow for creating `new` quotes both via UI and API,
please open an
[issue](https://github.com/dwyl/phoenix-content-negotiation-tutorial/issues).
We think it could be a _good_ idea to add `POST` endpoints as a "Bonus Level",
but we don't want to complicate things for the _first_ part of the tutorial.


Change into the `app` directory (`cd app`)
and open the project in your text editor (or IDE). <br />
e.g: `atom .`

#### 1.1 Check That Everything _Works_

_Before_ diving in to adding any features to our app,
let's check that it _works_. <br />
Run the server in your terminal:

```sh
mix phx.server
```

Then visit [`localhost:4000`](http://localhost:4000) in your web browser. <br />
You should see something like this (_the default Phoenix home page_):

![phoenix-homepage-default](https://user-images.githubusercontent.com/194400/81491226-cfa09480-9283-11ea-9d3b-4687b3f4aae4.png)


Having confirmed that the UI works,
let's run the tests:

```
mix test
```

You should see the following output in your terminal:

```
Generated app app
...

Finished in 0.02 seconds
3 tests, 0 failures
```

<br />


### 2. Add Quotes!

In order to display quotes in the UI/API we need a source of quotes.
Here's one we made earlier: https://hex.pm/packages/quotes

As per the instructions: https://github.com/dwyl/quotes#elixir
add the `quotes` dependency to `mix.exs`:

```elixir
{:quotes, "~> 1.0.5"}
```

> e.g [`mix.exs#L47`](https://github.com/dwyl/phoenix-content-negotiation-tutorial/blob/721b4c208e01e79ea9f2671cba13b515049f310b/mix.exs#L47)

Then run:

```sh
mix deps.get
```

That will download the `quotes` package which contains the
[`quotes.json`](https://github.com/dwyl/quotes/blob/master/quotes.json)
file
and Elixir functions to interact with it.

#### 2.1 Try It in `iex`!

In your terminal type:
```
iex -S mix
```

In the `iex` prompt type: `Quotes.random()` you will see a random quote.

```elixir
iex> Quotes.random()
%{
  "author" => "Lao Tzu",
  "text" => "If you would take, you must first give, this is the beginning of intelligence."
}
```

Great! So we know our quotes library is loaded into our Phoenix App. <br />
Quit `iex` and let's get back to building the App.


<br />


### 3. Generate the `Quotes` Controller, View, Templates and Tests

```
mix phx.gen.html Ctx Quotes quotes author:string text:string tags:string source:string --no-schema --no-context
```

> **Note**: `Ctx` is just an abbreviation for `Context`.
> We will _remove_ all references to `Ctx` in step 3.3 (below)
> because we _really_ don't need a
[`Context`](https://hexdocs.pm/phoenix/contexts.html)
> abstraction in a _simple_ example like this. ✂️

In your terminal, you should see the following output:

```
* creating lib/app_web/controllers/quotes_controller.ex
* creating lib/app_web/templates/quotes/edit.html.eex
* creating lib/app_web/templates/quotes/form.html.eex
* creating lib/app_web/templates/quotes/index.html.eex
* creating lib/app_web/templates/quotes/new.html.eex
* creating lib/app_web/templates/quotes/show.html.eex
* creating lib/app_web/views/quotes_view.ex
* creating test/app_web/controllers/quotes_controller_test.exs

Add the resource to your browser scope in lib/app_web/router.ex:

    resources "/quotes", QuotesController
```


> Git commit of files created in this step:
[9a37b21](https://github.com/dwyl/phoenix-content-negotiation-tutorial/commit/9a37b21192ae7360c59ae51d72ea9fd470f748e1)


#### 3.1 Add the Quotes Resources to `lib/app_web/router.ex`

Let's follow the instructions
given by the output of the `mix phx.gen.html` command
to add the resources to `lib/app_web/router.ex`.

Open the `router.ex` file
and locate the `scope "/", AppWeb do` block:

```elixir
scope "/", AppWeb do
  pipe_through :browser

  get "/", PageController, :index
end
```

add the following line to the block:

```elixir
resources "/quotes", QuotesController
```

> Your `router.ex` file should now look like this:
[`router.ex#L20`](https://github.com/dwyl/phoenix-content-negotiation-tutorial/blob/58e45a17ad44a2fee1cfef4e91b4dbede3a3b022/lib/app_web/router.ex#L20)


#### 3.2 Tidy Up: Delete Unused Files (_Optional_)

The `mix phx.gen.html` command creates a bunch of files
that are useful for "CRUD".
In our case we are not going to be creating or editing any quotes
as we already have our "bank" of quotes.
For simplicity we don't _want_ to run a Database for this example
so we can focus on rendering the content and not the "_management_".

Let's **`delete`** the files we don't _need_ so our project is tidy:

```
rm lib/app_web/templates/quotes/edit.html.eex
rm lib/app_web/templates/quotes/form.html.eex
rm lib/app_web/templates/quotes/new.html.eex
```

> Commit:
[`2d4ca13`](https://github.com/dwyl/phoenix-content-negotiation-tutorial/commit/2d4ca1375385a390d184a8b14f451ca249deef26)

<br />

#### 3.3 Compilation Error ... 🤷‍

_Sadly_, this `mix phx.gen` command
does not do _exactly_ what we expect. <br />
The `--no-context` flag does not _create_ a `context.ex` file,
but the
[quotes_controller.ex#L4-L5](https://github.com/dwyl/phoenix-content-negotiation-tutorial/blob/9a37b21192ae7360c59ae51d72ea9fd470f748e1/lib/app_web/controllers/quotes_controller.ex#L4-L5)
still has references to `Ctx`
and expects there to be an "implementation" of a Context.
That means that if we attempt to run the tests now they will fail:

```sh
mix test
```

You will see the following compilation error:

```sh
Compiling 18 files (.ex)

== Compilation error in file lib/app_web/controllers/quotes_controller.ex ==
** (CompileError) lib/app_web/controllers/quotes_controller.ex:13:
App.Ctx.Quotes.__struct__/1 is undefined, cannot expand struct App.Ctx.Quotes.
Make sure the struct name is correct. If the struct name exists and is correct
but it still cannot be found, you likely have cyclic module usage in your code
    (stdlib 3.11.2) lists.erl:1354: :lists.mapfoldl/3
    lib/app_web/controllers/quotes_controller.ex:12: (module)
    (stdlib 3.11.2) erl_eval.erl:680: :erl_eval.do_apply/6
```

> We opened an issue to clarify the behaviour:
https://github.com/phoenixframework/phoenix/issues/3832
![chris-closes-issue](https://user-images.githubusercontent.com/194400/81950875-678fdc80-95fc-11ea-8eb3-2b7c0d408a6d.png) <br />
Turns out that "_generators are first and foremost learning tools_",
fair enough. <br />
If the generator doesn't do _exactly_ what we expect,
we just work _around_ it.

<br />

Let's make a few of quick updates
to the `quotes_controller_test.exs`,
`quotes_controller.ex` and
`index.html.eex` files
to avoid this compilation error.


The tests created by `mix phx.gen.html`
assume we are building a standard "CRUD" interface; we aren't.
So we need to **`delete`** those irrelevant tests
and replace them.
Open the file `test/app_web/controllers/quotes_controller_test.exs`
and replace the contents with the following code:

```elixir
defmodule AppWeb.QuotesControllerTest do
  use AppWeb.ConnCase

  describe "/quots" do
    test "shows a random quote", %{conn: conn} do
      conn = get(conn, Routes.quotes_path(conn, :index))
      assert html_response(conn, 200) =~ "Quote"
    end
  end

end
```

> Before:
[`quotes_controller_test.exs`](https://github.com/dwyl/phoenix-content-negotiation-tutorial/blob/2d4ca1375385a390d184a8b14f451ca249deef26/test/app_web/controllers/quotes_controller_test.exs) <br />
> After:
[`quotes_controller_test.exs`](https://github.com/dwyl/phoenix-content-negotiation-tutorial/blob/77c3310a2614fbf7db1570a8eb501ee87cc9baa0/test/app_web/controllers/quotes_controller_test.exs)


Open the `lib/app_web/controllers/quotes_controller.ex`
and replace the contents with the following:

```elixir
defmodule AppWeb.QuotesController do
  use AppWeb, :controller

  # transform map with keys as strings into keys as atoms!
  # https://stackoverflow.com/questions/31990134
  def transform_string_keys_to_atoms(map) do
    for {key, val} <- map, into: %{}, do: {String.to_existing_atom(key), val}
  end

  def index(conn, _params) do
    q = Quotes.random() |> transform_string_keys_to_atoms
    render(conn, "index.html", quote: q)
  end
end
```

> Before:
[`quotes_controller.ex`](https://github.com/dwyl/phoenix-content-negotiation-tutorial/blob/2d4ca1375385a390d184a8b14f451ca249deef26/lib/app_web/controllers/quotes_controller.ex) <br/>
> After:
[`quotes_controller.ex`](https://github.com/dwyl/phoenix-content-negotiation-tutorial/blob/77c3310a2614fbf7db1570a8eb501ee87cc9baa0/lib/app_web/controllers/quotes_controller.ex)


Finally, open the `lib/app_web/templates/quotes/index.html.eex` file
and replace the contents with this code:

```html
<h1>Quotes</h1>
<p>"<strong><em><%= @quote.text %></em></strong>" ~ <%= @quote.author %></p>
````

> Before:
[`quotes/index.html.eex`](https://github.com/dwyl/phoenix-content-negotiation-tutorial/blob/2d4ca1375385a390d184a8b14f451ca249deef26/lib/app_web/templates/quotes/index.html.eex) <br/>
> After:
[`quotes/index.html.eex`](https://github.com/dwyl/phoenix-content-negotiation-tutorial/blob/77c3310a2614fbf7db1570a8eb501ee87cc9baa0/lib/app_web/templates/quotes/index.html.eex)

Now re-run the tests:

```
mix test
```

You should see them pass:

```
Compiling 3 files (.ex)
....

Finished in 0.07 seconds
4 tests, 0 failures

Randomized with seed 115090
```



Let's do a quick visual check.
Run the Phoenix server:

```sh
mix phx.server
```

Then visit [`localhost:4000`](http://localhost:4000) in your web browser. <br />
You should see a random quotation:


![quotes-rendered-html-working](https://user-images.githubusercontent.com/194400/81924207-b591e980-95d6-11ea-883b-03aee2e2acea.png)

With tests passing again and a random quote rendering,
let's attempt to make a JSON request to the `HTML` endpoint
(_and see it fail_).


<br />

#### 3.4 Content Negotiation _Fails_


At this stage if we run the server (`mix phx.server`)
and attempt to make a request to the `/quotes` endpoint
(_in a different terminal window_)
with a JSON `Accepts` header:

```
curl -i -H "Accept: application/json" http://localhost:4000/quotes
```

We will see the following error: <br />

```md
HTTP/1.1 406 Not Acceptable
cache-control: max-age=0, private, must-revalidate
content-length: 1915
date: Fri, 15 May 2020 07:44:44 GMT
server: Cowboy
x-request-id: Fg8j6sIqqtAKLiIAAAGB

# Phoenix.NotAcceptableError at GET /quotes

Exception:

    ** (Phoenix.NotAcceptableError) no supported media type in accept header.

    Expected one of ["html"] but got the following formats:

      * "application/json" with extensions: ["json"]

    To accept custom formats, register them under the :mime library
    in your config/config.exs file:

        config :mime, :types, %{
          "application/xml" => ["xml"]
        }

    And then run `mix deps.clean --build mime` to force it to be recompiled.

        (phoenix 1.5.1) lib/phoenix/controller.ex:1313: Phoenix.Controller.refuse/3
        (app 0.1.0) AppWeb.Router.browser/2
        (app 0.1.0) lib/app_web/router.ex:1: AppWeb.Router.__pipe_through0__/1
        (phoenix 1.5.1) lib/phoenix/router.ex:347: Phoenix.Router.__call__/2
        (app 0.1.0) lib/app_web/endpoint.ex:1: AppWeb.Endpoint.plug_builder_call/2
        (app 0.1.0) lib/plug/debugger.ex:132: AppWeb.Endpoint."call (overridable 3)"/2
        (app 0.1.0) lib/app_web/endpoint.ex:1: AppWeb.Endpoint.call/2
        (phoenix 1.5.1) lib/phoenix/endpoint/cowboy2_handler.ex:64: Phoenix.Endpoint.Cowboy2Handler.init/4


## Connection details

### Params

    %{}

### Request info

  * URI: http://localhost:4000/quotes
  * Query string:

### Headers

  * accept: application/json
  * host: localhost:4000
  * user-agent: curl/7.64.1

### Session

    %{}
```


And in the terminal running the `phx.server`,
you will see: <br />

```md
[debug] ** (Phoenix.NotAcceptableError) no supported media type in accept header.

Expected one of ["html"] but got the following formats:

  * "application/json" with extensions: ["json"]

To accept custom formats, register them under the :mime library
in your config/config.exs file:

    config :mime, :types, %{
      "application/xml" => ["xml"]
    }

And then run `mix deps.clean --build mime` to force it to be recompiled.

    (phoenix 1.5.1) lib/phoenix/controller.ex:1313: Phoenix.Controller.refuse/3
    (app 0.1.0) AppWeb.Router.browser/2
    (app 0.1.0) lib/app_web/router.ex:1: AppWeb.Router.__pipe_through0__/1
    (phoenix 1.5.1) lib/phoenix/router.ex:347: Phoenix.Router.__call__/2
    (app 0.1.0) lib/app_web/endpoint.ex:1: AppWeb.Endpoint.plug_builder_call/2
    (app 0.1.0) lib/plug/debugger.ex:132: AppWeb.Endpoint."call (overridable 3)"/2
    (app 0.1.0) lib/app_web/endpoint.ex:1: AppWeb.Endpoint.call/2
    (phoenix 1.5.1) lib/phoenix/endpoint/cowboy2_handler.ex:64: Phoenix.Endpoint.Cowboy2Handler.init/4
```



This is understandable given that the app doesn't
have any pipeline/route that accepts JSON requests. <br />
Let's get on with the content negotiation part!


<br />


### 4. Create a Content Negotiation Pipeline in `router.ex`

By default the Phoenix router separates
the `:browser` pipeline (which accepts `"html"`)
from the `:api` (which accepts `"json"`):


```elixir
defmodule AppWeb.Router do
  use AppWeb, :router

  pipeline :browser do
    plug :accepts, ["html"]
    plug :fetch_session
    plug :fetch_flash
    plug :protect_from_forgery
    plug :put_secure_browser_headers
  end

  pipeline :api do
    plug :accepts, ["json"]
  end

  scope "/", AppWeb do
    pipe_through :browser

    get "/", PageController, :index
    resources "/quotes", QuotesController
  end

  # Other scopes may use custom stacks.
  # scope "/api", AppWeb do
  #   pipe_through :api
  # end
end
```

By default the `/api` scope is commented out.
We are _not_ going to enable it,
rather as per our goal (above)
we want to have the API and UI
handled by the _same_ router pipeline.

Let's _replace_ the code in the `router.ex` with the following:

```elixir
defmodule AppWeb.Router do
  use AppWeb, :router

  pipeline :any do
    plug :negotiate
  end

  defp negotiate(conn, []) do
    {"accept", accept} = List.keyfind(conn.req_headers, "accept", 0)

    if accept =~ "json" do # don't do anything for JSON (API) requests:
      conn
    else # setup conn for HTML requests:
      conn
      |> fetch_session([])
      |> fetch_flash([])
      |> protect_from_forgery([])
      |> put_secure_browser_headers([])
    end
  end

  scope "/", AppWeb do
    pipe_through :any

    get "/", PageController, :index
    resources "/quotes", QuotesController
  end
end
```

In this code we are replacing the `:browser` pipeline
with the `:any` pipeline that handles all types of content.
The `:any` pipeline invokes `:negotiate`
which is defined immediately below.

In `negotiate/2` we simply check the `accept` header
in `conn.req_headers`.
If the `accept` header matches the string `"json"`,
we don't need to do any further setup,
otherwise we assume the request expects an `HTML` response
invoke the appropriate plugs that were in the `:browser` pipeline.

> **Note**: we _know_ this is not "production" code.
This is just an ["MVP"](https://en.wikipedia.org/wiki/Minimum_viable_product)
for how to do content negotiation.
We will improve it below!


At the end of this step, your router file should look like this:
[`router.ex`](https://github.com/dwyl/phoenix-content-negotiation-tutorial/blob/f4edaac782bb1bc5d8f55f4b8677212483b1da0a/lib/app_web/router.ex)


<br />

### 5. Handle `JSON` requests in `QuotesController`

Now that our `router.ex` pipeline
is setup to accept _any_ content type,
we need to _handle_ the request for JSON in our controller.

Open the `lib/app_web/controllers/quotes_controller.ex` file
and update the `index/2` function with the following:

```elixir
def index(conn, _params) do
  q = Quotes.random() |> transform_string_keys_to_atoms
  {"accept", accept} = List.keyfind(conn.req_headers, "accept", 0)

  if accept =~ "json" do
    json(conn, q)
  else
    render(conn, "index.html", quote: q)
  end
end


```

Here we use the `Phoenix.Controller`
[json/2](https://hexdocs.pm/phoenix/Phoenix.Controller.html#json/2)
to sends a JSON response.

It uses the configured
[`:json_library`](https://github.com/dwyl/phoenix-content-negotiation-tutorial/blob/f4edaac782bb1bc5d8f55f4b8677212483b1da0a/config/config.exs#L24)
(`Jason`)
under the `:phoenix` application
for `:json` to pick up the encoder module.

<br />

At this point our rudimentary content negotiation is _working_.
Try it: run the Phoenix server:


```sh
mix phx.server
```

In a different terminal window/tab, run the
[`cURL`](https://en.wikipedia.org/wiki/CURL)
command:

```sh
curl -i -H "Accept: application/json" http://localhost:4000/quotes
```

You should see output similar to this:

```
HTTP/1.1 200 OK
cache-control: max-age=0, private, must-revalidate
content-length: 86
content-type: application/json; charset=utf-8
date: Sat, 16 May 2020 14:25:51 GMT
server: Cowboy
x-request-id: Fg-IYvb_4_U9xvYAAASh

{"author":"Johann Wolfgang von Goethe","text":"Knowing is not enough; we must apply!"}
```

If you prefer to _just_ have the JSON response, omit the `-i` flag:

```sh
curl -H "Accept: application/json" http://localhost:4000/quotes
```

Now you will just see the quote `text` and `author`
(_and where available, `tags` and `source`_):

```json
{
  "author":"Ernest Hemingway",
  "source":"https://www.goodreads.com/quotes/353013",
  "tags":"listen, learn, learning",
  "text":"I like to listen. I have learned a great deal from listening carefully. Most people never listen."
}
```


Confirm that it still works in the browser:
[http://localhost:4000/quotes](http://localhost:4000/quotes)

![image](https://user-images.githubusercontent.com/194400/82122061-1fea8b80-9789-11ea-922f-3d671ac35a89.png)


<br />

#### 5.1 Fix Failing Tests!

While the content negotiation works
for returning `HTML` and `JSON`,
the changes we have made will break the tests.

If you try to run the tests now you will see them fail:

```
mix test
```

```
1) test /quotes shows a random quote (AppWeb.QuotesControllerTest)
   test/app_web/controllers/quotes_controller_test.exs:5
   ** (MatchError) no match of right hand side value: nil
   code: |> get(Routes.quotes_path(conn, :index))
   stacktrace:
     (app 0.1.0) lib/app_web/router.ex:9: AppWeb.Router.negotiate/2
     (app 0.1.0) AppWeb.Router.any/2
     (app 0.1.0) lib/app_web/router.ex:1: AppWeb.Router.__pipe_through0__/1
```

This fails because we are attempting to get the `"accept"` header
in the `router.ex` `negotiate/2` function
but there are no headers defined in our test!


In Plug (and thus Phoenix) tests,
no headers are set by default. <br />
This is the output of inspecting the `conn`
(`IO.inspect(conn)`):

```elixir
%Plug.Conn{
  adapter: {Plug.Adapters.Test.Conn, :...},
  assigns: %{},
  before_send: [],
  body_params: %Plug.Conn.Unfetched{aspect: :body_params},
  cookies: %Plug.Conn.Unfetched{aspect: :cookies},
  halted: false,
  host: "www.example.com",
  method: "GET",
  owner: #PID<0.335.0>,
  params: %Plug.Conn.Unfetched{aspect: :params},
  path_info: [],
  path_params: %{},
  port: 80,
  private: %{phoenix_recycled: true, plug_skip_csrf_protection: true},
  query_params: %Plug.Conn.Unfetched{aspect: :query_params},
  query_string: "",
  remote_ip: {127, 0, 0, 1},
  req_cookies: %Plug.Conn.Unfetched{aspect: :cookies},
  req_headers: [],
  request_path: "/",
  resp_body: nil,
  resp_cookies: %{},
  resp_headers: [{"cache-control", "max-age=0, private, must-revalidate"}],
  scheme: :http,
  script_name: [],
  secret_key_base: nil,
  state: :unset,
  status: nil
}
```

The important line is:

```elixir
req_headers: [],
```

`req_headers` is an _empty_ List.

There are two ways of fixing this failing test:

a. We include the right `"accept"` header in each test. <br />
b. We set a `default` value if there is no `"accept"` header defined.

If we go with the first option,
we will need to add an accept header in the test:

```elixir
test "shows a random quote", %{conn: conn} do
  conn =
    conn
    |> put_req_header("accept", "text/html")
    |> get(Routes.quotes_path(conn, :index))

  assert html_response(conn, 200) =~ "Quote"
end
```

This is fine in an individual case,
but it will get old if we are using content negotiation
in a more sophisticated app with dozens of routes.

We _prefer_ to create a helper function
that sets a default value if no `accept` header is set.
Open the `lib/app_web/controllers/quotes_controller.ex` file
and add the following helper function:

```elixir
@doc """
`get_accept_header/2` gets the "accept" header from req_headers.
Defaults to "text/html" if no header is set.
"""
def get_accept_header(conn) do
  case List.keyfind(conn.req_headers, "accept", 0) do
    {"accept", accept} ->
      accept

    nil ->
      "tex/html"
  end
end
```

We can now _use_ this function
in both our `AppWeb.QuotesController.index/2`
and `AppWeb.Router.negotiate/2` functions:


With the `lib/app_web/controllers/quotes_controller.ex` file still open,
update the `index/2` function to:

```elixir
def index(conn, _params) do
  q = Quotes.random() |> transform_string_keys_to_atoms

  if get_accept_header(conn) =~ "json" do
    json(conn, q)
  else
    render(conn, "index.html", quote: q)
  end
end
```

And in `router.ex` update the `negotiate/2` function to:

```elixir
defp negotiate(conn, []) do
  if AppWeb.QuotesController.get_accept_header(conn) =~ "json" do
    conn
  else
    conn
    |> fetch_session([])
    |> fetch_flash([])
    |> protect_from_forgery([])
    |> put_secure_browser_headers([])
  end
end
```

Now re-run the tests and they will pass:




<br /> <br />

## Notes & Observations

<br />

### Q: Is there an _Official_ Way of Doing Content Negotiation?

While there is no "_official_" guide in the docs
for how to do content negotiation,
there is an issue/thread where it is discussed:
[phoenix/issues/1054](https://github.com/phoenixframework/phoenix/issues/1054)

Both [José Valim](https://github.com/josevalim)
the creator of `Elixir` and
[Chris McCord](https://github.com/chrismccord)
creator of `Phoenix` have given input in the issue.
So we have a fairly good notion that this is
the _acceptable_ way of doing content negotiation in a Phoenix App.

José outlines the Plug approach
(_this is what we did in **step 4** above_):
![josevalim-plug-router](https://user-images.githubusercontent.com/194400/81637506-7add5500-940e-11ea-8a7f-200268d34946.png)

Chris advises to use `Phoenix.Controller.get_format` and pattern matching:
![chris-pattern-matching](https://user-images.githubusercontent.com/194400/81637373-0bfffc00-940e-11ea-8ccd-e42b048bef42.png)

This is relevant for the general use case but is not to our _specific_ one.


Chris also created a Gist:
https://gist.github.com/chrismccord/31340f08d62de1457454 <br />
Which shows how to do content negotiation based on `params.format`.
We have used this approach into our tutorial.

<br />

> **Note**: _this_ issue
[phoenix/issues/1054](https://github.com/phoenixframework/phoenix/issues/1054)
is a textbook example of _why_
we open issues to ask questions. <br />
The thread shows the initial uncertainty of the original poster. <br />
There is a _discussion_ for why content negotiation is necessary
and suggested approaches for doing it. <br />
Finally there is a comment from a person
who discovered the issue _years_ later
and found the thread useful. <br />
3 years later we are using it as the basis for our solution! <br />
In the future others will stumble upon it
and be grateful that it exists. <br />
Open issues with questions!
It's the _right_ thing to do to learn and discuss all topics. <br />
Both people in your team and complete strangers will benefit!
