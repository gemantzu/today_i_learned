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

### Users scaffold
```terminal
mix phx.gen.html Auth User users email provider token
```

### Controller for OAuth
```elixir
defmodule Appname.Web.AuthController do
  use Memkado.Web, :controller
  plug Ueberauth
  alias Memkado.Auth

  def callback(%{assigns: %{ueberauth_auth: auth}} = conn, params) do
    user_params = %{
      token: auth.credentials.token,
      email: auth.info.email,
      provider: params["provider"]
    }
    signin(conn, user_params)
  end
  
  
  def signout(conn, _params) do
    conn
    |> configure_session(drop: true)
    |> redirect(to: page_path(conn, :index))
  end

  defp signin(conn, user_params) do
    case insert_or_find_user(user_params) do
      {:ok, user}->
        conn
        |> put_flash(:info, "Welcome back!")
        |> put_session(:user_id, user.id)
        |> redirect(to: card_path(conn, :index))
      {:error, reason} ->
        conn
        |> put_flash(:error, "Error: #{reason}")
        |> redirect(to: page_path(conn, :index))
    end
  end

  defp insert_or_find_user(user_params) do
    case Auth.get_user_by_email(user_params.email) do
      nil ->
        Auth.create_user(user_params)
      user ->
        Auth.update_user(user, user_params)
    end
  end
end
```

### Router paths
```elixir
pipeline :browser do
  ...
  plug Appname.Web.Plugs.SetUser
end

scope "/", Appname.Web do
...
  resources "/users", UserController
end
scope "/auth", Appname.Web do
  pipe_through :browser
  
  get "/signout", AuthController, :signout
  get "/:provider", AuthController, :callback
  get "/:provider/callback", AuthController, :callback
end
```

### Appname.Web.Plugs.SetUser plug
```elixir
defmodule Appname.Web.Plugs.SetUser do
  import Plug.Conn
  import Phoenix.Controller
  alias Appname.Auth

  def init(_params) do
  end

  def call(conn, _) do
    user = get_session(conn, :user_id)

    cond do
      user = user_id && Auth.get_user!(user_id) ->
        assign(conn, :current_user, user)
      true ->
        assign(conn, :current_user, nil)
    end
  end
end

```

### Links to login / logout (layout)
```elixir
<%= if @conn.assigns[:current_user] do %>
  <li><%= link "Sign out", to: auth_path(@conn, :signout) %></li>
<% else %>
  <li><%= link "Sign in", to: auth_path(@conn, :request, "github") %></li>
<% end %>
```
