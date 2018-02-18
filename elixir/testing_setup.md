Setup needed to run `mix test` in the proper environment, with correct config & database setup.

## mix.exs (add `elixirc_paths` and `aliases`)

```elixir
def project do
  [
    ...
    elixirc_paths: elixirc_paths(Mix.env),
    aliases: aliases(),
    ...
  ]
end

...

defp elixirc_paths(:test), do: ["lib", "test/support"]
defp elixirc_paths(_), do: ["lib"]

defp aliases do
  [
    ...,
    "test": ["ecto.drop --quiet", "ecto.create --quiet", "ecto.migrate --quiet", "test"],
  ]
end
...
```

## config/config.exs

Uncomment 
```elixir
import_config "#{Mix.env()}.exs"
```

## config/test.exs (new file)

```elixir
use Mix.Config

config :app, App.Repo,
  adapter: Ecto.Adapters.Postgres,
  database: "app_test",
  username: "postgres",
  password: "postgres",
  hostname: "localhost",
  pool: Ecto.Adapters.SQL.Sandbox

```
