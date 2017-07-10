## Deps

```elixir
defp deps do
  [
    {:hound, "~> 1.0"}
  ]
end
```

```shell
mix deps.get
```

## Test Helper ensure_all_started/1

```elixir
# test/test_helper.exs
Application.ensure_all_started(:hound)

ExUnit.start
```

## Enable server running on test

```elixir
# config/test.exs
use Mix.Config

config :app_name, AppName.Endpoint,
  http: [port: 4001],
  server: true

```

## Config hound on test

```elixir
config :hound,
  driver: "phantomjs"
```

## Install and run phantomjs

```shell
phantomjs --wd
```

## Integration / Acceptance TestCase Module

```elixir
# test/support/integration_case.ex
# solution found [here!](http://codeloveandboards.com/blog/2016/01/29/integration-tests-fun-with-phoenix-and-react/)

defmodule AppName.IntegrationCase do
  use ExUnit.CaseTemplate
  use Hound.Helpers

  using do
    quote do
      use Hound.Helpers

      import Ecto, only: [build_assoc: 2]
      import Ecto.Schema
      import Ecto.Query, only: [from: 2]
      import AppName.Router.Helpers
      import AppName.IntegrationCase

      alias AppName.Repo

      # The default endpoint for testing
      @endpoint AppName.Endpoint

      hound_session()
    end
  end

  setup tags do
    :ok = Ecto.Adapters.SQL.Sandbox.checkout(AppName.Repo)

    unless tags[:async] do
      Ecto.Adapters.SQL.Sandbox.mode(AppName.Repo, {:shared, self()})
    end

    :ok
  end
end
```

## Write an Integration Test

```elixir
# test/integration/products_test.exs
defmodule AppName.Integration.ProductsTest do
  use AppName.IntegrationCase
  
  test "/products shows a list of products with the id `products`" do
    navigate_to "/products"
    assert find_element(:css, "ul#products")
  end
end
```

## Proceed with regular workflow (route, controller, view, template)
