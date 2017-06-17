### Add Uberauth to your mix.exs file
```elixir
defp deps do
[
  ...
  {:ueberauth, "~> 0.4"},
  {:ueberauth_github, "~> 0.4"}
]
```

### Register your application with Github (or any other provider) and give a callback route eg `/auth/github/callback`

### Config Ueberauth in your config/config.exs file
```elixir
config :ueberauth, Ueberauth,
  providers: [
    github: { Ueberauth.Strategy.Github, [] }
  ]

config :ueberauth, Ueberauth.Strategy.Github.OAuth,
  client_id: "..",
  client_secret: ".."
```
### Controller for OAuth

```elixir
defmodule Appname.Web.AuthController do
  use Appname.Web, :controller
  plug Ueberauth
  
  def callback(conn, _params) do
  end
end
```

### Router paths
```elixir
scope "/auth", Appname.Web do
  pipe_through :browser
  
  get "/:provider", AuthController, :callback
  get "/:provider/callback", AuthController, :callback
end
```

# tbc
