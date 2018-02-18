I have to use Sandbox mode in order to run async tests (and prevent tests from failind due to race condiditons)

### config/test.exs
```elixir
config :app, App.Repo,[
  adapter: Ecto.Adapters.Postgres,
  database: "app_test",
  username: "postgres",
  password: "postgres",
  hostname: "localhost",
  pool: Ecto.Adapters.SQL.Sandbox
]
```

### test/test_helper.exs
```elixir
ExUnit.start()
Ecto.Adapters.SQL.Sandbox.mode(Todos.Repo, :manual)
```

### test_file.exs
```elixir
setup do
  :ok = Ecto.Adapters.SQL.Sandbox.checkout(Repo)
end
```
